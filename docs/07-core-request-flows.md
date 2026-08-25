# 黑马点评核心业务调用链索引

> 目的：以后学习任何一个模块时，先找到“请求从哪儿进、业务在哪儿做、Redis/MySQL 在哪儿读写”。

## 1. 登录：发送验证码

前端调用：

```text
POST /user/code
```

调用链：

```text
UserController.sendCode(phone, session)
  ↓
IUserService.sendCode(...)
  ↓
UserServiceImpl.sendCode(...)
  ↓
RegexUtils 校验手机号
  ↓
RandomUtil 生成 6 位验证码
  ↓
StringRedisTemplate.opsForValue().set(...)
  ↓
Redis 保存验证码 + TTL
  ↓
Result.ok()
```

核心 Redis 形态：

```text
key   = LOGIN_CODE_KEY + phone
value = 验证码
TTL   = 若干分钟
```

这里先观察一个重要变化：

```text
传统方案：验证码存在 HttpSession
              ↓
改造后：验证码存在 Redis
```

原因不是“为了用 Redis”，而是为多实例部署下共享登录相关数据做准备。

---

## 2. 登录：验证码登录并生成 token

接口：

```text
POST /user/login
```

调用链：

```text
UserController.login(LoginFormDTO, session)
  ↓
UserServiceImpl.login(...)
  ↓
校验手机号格式
  ↓
Redis 读取验证码
  ↓
比较用户提交验证码
  ↓
MySQL 按手机号查询 User
  ↓
不存在则创建用户
  ↓
生成随机 token
  ↓
User → UserDTO
  ↓
UserDTO → Map<String,Object>
  ↓
Redis Hash 保存登录用户
  ↓
设置登录 TTL
  ↓
把 token 返回给前端
```

需要重点理解：

```text
手机号不是登录态凭证
验证码也不是登录态凭证

真正后续反复携带的是 token
```

Redis 中可以想成：

```text
login:token:abc123
  id       -> 1001
  nickName -> user_xxx
  icon     -> ...
```

也就是：

```text
token → UserDTO
```

---

## 3. 已登录用户：请求如何恢复当前用户

用户登录成功后，后续请求不会每次重新执行登录逻辑。

关键链路：

```text
HTTP Request
  ↓
RefreshTokenInterceptor
  ↓
从请求 Header 获取 token
  ↓
Redis 查询 LOGIN_USER_KEY + token
  ↓
Hash → UserDTO
  ↓
UserHolder.saveUser(userDTO)
  ↓
刷新 Redis TTL
  ↓
Controller
```

这里第一次真正遇到：

```text
Redis + Interceptor + ThreadLocal
```

三者分工：

- Redis：跨请求、跨服务器保存登录态
- Interceptor：每次请求自动恢复登录态
- ThreadLocal：在本次请求线程内方便获取当前用户

---

## 4. 必须登录的接口：LoginInterceptor

对于必须登录才能访问的接口：

```text
Request
  ↓
RefreshTokenInterceptor
  ↓
LoginInterceptor
  ↓
UserHolder.getUser()
  ├─ 有用户 → 放行
  └─ 无用户 → 返回 401
```

这里特别容易混淆：

```text
RefreshTokenInterceptor
不是“阻止未登录用户”
而是“尝试恢复登录用户 + 刷新 TTL”

LoginInterceptor
才负责“这个接口必须登录”
```

---

## 5. 查询商户：缓存主线

典型业务入口：

```text
GET /shop/{id}
```

学习时重点顺：

```text
ShopController
  ↓
ShopServiceImpl
  ↓
先查 Redis
  ↓
命中？
  ├─ 是 → 返回缓存
  └─ 否 → 查 MySQL
              ↓
            回写 Redis
              ↓
             返回
```

随后课程会在这条基本链路上逐步加入：

- 缓存空值
- TTL
- 缓存穿透
- 互斥锁
- 缓存击穿
- 逻辑过期

所以商户缓存不要拆成很多孤立技巧，而要理解为：

> 对同一条“查询商户”链路不断增强可靠性。

---

## 6. 秒杀下单：最值得反复画图的一条链

入口通常从：

```text
VoucherOrderController
  ↓
VoucherOrderServiceImpl
```

学习过程会经历多个版本。

### 第一版：直接数据库下单

```text
检查秒杀时间
↓
查库存
↓
扣库存
↓
创建订单
```

问题逐步暴露：

```text
高并发
→ 超卖
→ 一人多单
→ JVM 锁在多实例下失效
→ 数据库压力仍然大
```

### 后续增强版

```text
请求
↓
Lua 在 Redis 中一次性判断：
  - 库存是否足够
  - 是否一人一单
↓
Redis 预扣库存
↓
把订单消息写入 Stream
↓
快速返回订单 ID

后台消费者
↓
读取 Stream
↓
真正创建数据库订单
```

最终要形成一个核心认识：

> Redis 不只是缓存。在黑马点评秒杀模块里，它还承担高并发下的原子判断、库存预处理和消息队列角色。

---

## 7. 点赞与排行榜

入口：

```text
BlogController
  ↓
BlogServiceImpl
```

核心 Redis：

```text
Sorted Set
```

为什么不用普通 Set？

因为需求不只是：

```text
某用户有没有点赞
```

还需要：

```text
谁先点赞 / 最近点赞用户 / 排序
```

所以 score 可以承担时间相关排序信息。

---

## 8. Feed 流

关注用户发布博客后，需要让粉丝看到新内容。

核心思路：

```text
作者发布 Blog
↓
找到粉丝
↓
向粉丝 Feed 的 Sorted Set 推送 blogId
↓
粉丝查询 Feed
↓
按 score 做滚动分页
```

这里值得重点区分：

```text
普通分页：page + size
滚动分页：maxScore + offset
```

原因是 Feed 会持续插入新数据，普通页码分页容易发生数据漂移。

---

## 9. 签到：BitMap

入口：

```text
POST /user/sign
GET  /user/sign/count
```

调用：

```text
UserController
  ↓
UserServiceImpl
  ↓
UserHolder 获取当前用户
  ↓
根据 yyyy:MM:userId 构造 Redis key
  ↓
setBit / bitField
```

理解重点：

一个月的签到，本质可以表示为：

```text
1 1 0 1 1 1 0 ...
```

每天只需要 1 bit，而不是一整条对象记录。

---

## 10. 推荐先真正吃透的前三条链

不要同时追十个模块。

第一阶段只画熟：

```text
① POST /user/code
② POST /user/login
③ 登录后的任意请求如何经拦截器恢复 UserHolder
```

这三条讲顺以后，再进入：

```text
④ GET /shop/{id} 的缓存链
⑤ 秒杀下单链
```

这会比按文件数量推进稳定得多。
