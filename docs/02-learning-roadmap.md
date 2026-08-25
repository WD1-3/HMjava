# 黑马点评学习路线

## 阶段 0：把项目跑起来

目标：确认环境和源码没有问题。

- Clone 项目
- 切换 `init`
- 导入 `hmdp.sql`
- 配置 MySQL
- 配置 Redis
- 启动后端
- 打开前端

完成标准：

> 能看到页面，后端接口能正常响应，知道配置文件在哪里。

---

## 阶段 1：认识项目骨架

先回答：

- Controller 在哪里？
- Service 在哪里？
- Mapper 在哪里？
- Entity / DTO / VO 分别是什么？
- 配置文件在哪里？
- Redis 相关工具类在哪里？

然后选一条最简单的请求，完整走一次：

```text
HTTP 请求
→ Controller
→ Service
→ Mapper / Redis
→ 返回 DTO / Result
```

完成标准：

> 不看教程，也能指出一次请求主要经过哪些层。

---

## 阶段 2：短信登录与 Redis Session

重点：

- 验证码保存在哪里
- 登录成功后 token 如何产生
- 用户信息如何写入 Redis
- 请求如何根据 token 恢复登录用户
- 为什么不用传统单机 Session

Java / Spring 写法重点：

- Controller 参数绑定
- Service 层调用
- DTO
- 拦截器
- ThreadLocal
- StringRedisTemplate

---

## 阶段 3：商户缓存

重点：

- Cache Aside
- 缓存穿透
- 缓存雪崩
- 缓存击穿
- 空值缓存
- 逻辑过期
- 互斥锁

这一阶段必须把“问题出现的原因”讲清楚，再记解决方案。

---

## 阶段 4：优惠券秒杀

重点：

- 库存扣减
- 超卖
- 乐观锁
- 一人一单
- 事务
- 高并发场景下数据库压力

---

## 阶段 5：分布式锁

重点：

- synchronized 为什么不够
- SETNX
- 锁误删
- UUID / 线程标识
- Lua 原子解锁
- Redisson
- WatchDog

---

## 阶段 6：Redis Stream 与异步下单

重点：

- Lua 前置校验
- 下单消息入 Stream
- Consumer Group
- Pending List
- 异步订单处理

目标不是只会调用 API，而是理解：

> 为什么把“资格判断”和“真正写数据库”拆开。

---

## 阶段 7：社交功能

模块：

- 点赞
- Top 排行
- 关注
- 共同关注
- Feed 流
- 滚动分页

对应 Redis：

- Set
- Sorted Set

---

## 阶段 8：Redis 高级数据结构业务

### GEO

附近商户。

### BitMap

签到统计。

### HyperLogLog

UV 统计。

完成标准：

> 能说清楚“为什么这个业务适合这个数据结构”。

