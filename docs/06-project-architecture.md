# 黑马点评项目架构：先看懂各层在干什么

> 本文以 `cs001020/hmdp` 的 `master` 分支为源码参照。
>
> 目标不是背目录，而是建立一个稳定的“看到文件名就知道它大概负责什么”的项目地图。

## 1. 后端主目录

核心 Java 代码位于：

```text
src/main/java/com/hmdp/
```

源码中的主要目录包括：

```text
com.hmdp
├── HmDianPingApplication.java
├── config/
├── controller/
├── dto/
├── entity/
├── interceptor/
├── mapper/
├── service/
└── utils/
```

先用一句话理解：

```text
HTTP 请求
  ↓
Controller：接请求
  ↓
Service：做业务
  ↓
Mapper：操作 MySQL
  ↘
   Redis：缓存 / 登录状态 / 高并发数据结构
```

---

## 2. `controller/`：请求入口

Controller 是最适合开始读项目的地方。

典型文件：

- `UserController.java`：用户、登录、签到
- `ShopController.java`：商户
- `VoucherController.java`：优惠券
- `VoucherOrderController.java`：优惠券订单 / 秒杀入口
- `BlogController.java`：探店笔记、点赞、Feed
- `FollowController.java`：关注关系
- `ShopTypeController.java`：商户分类

新手读源码时不要从 ServiceImpl 随机开始。

优先：

```text
前端动作
→ 找接口
→ 找 Controller 方法
→ 再跟进 Service
```

---

## 3. `service/` 与 `service/impl/`：业务核心

`service/` 通常放接口，`service/impl/` 放具体实现。

黑马点评里真正最值得读的实现文件包括：

- `UserServiceImpl.java`
- `ShopServiceImpl.java`
- `VoucherOrderServiceImpl.java`
- `BlogServiceImpl.java`
- `FollowServiceImpl.java`
- `ShopTypeServiceImpl.java`

其中几个重量级文件：

### `UserServiceImpl`

主要学习：

- 手机验证码
- Redis 登录状态
- token
- BitMap 签到

### `ShopServiceImpl`

主要学习：

- 缓存
- Cache Aside
- 缓存穿透
- 缓存击穿
- 逻辑过期
- GEO 查询

### `VoucherOrderServiceImpl`

主要学习：

- 秒杀
- 一人一单
- 乐观锁
- 分布式锁
- Lua
- Redis Stream
- 异步下单

### `BlogServiceImpl`

主要学习：

- 点赞
- Sorted Set
- Feed 流
- 滚动分页

---

## 4. `mapper/`：数据库访问

Mapper 是 MyBatis-Plus 和 MySQL 之间的连接层。

阅读时先形成一个简单认识：

```text
Service
  ↓
Mapper / MyBatis-Plus
  ↓
MySQL 表
```

初期不必把每个 Mapper 都展开研究。

先重点观察：

- Service 什么时候直接调用 `getById()` / `save()` / `update()`
- 什么时候使用 `baseMapper`
- 什么时候需要 LambdaQuery / LambdaUpdate

---

## 5. `entity/`：数据库实体

Entity 基本对应数据库表。

例如：

```text
User
Shop
Voucher
SeckillVoucher
VoucherOrder
Blog
Follow
```

读到某个 Service 里的字段不知道是什么时，再打开对应 Entity 即可。

不要把 Entity 当作学习起点。

---

## 6. `dto/`：接口层需要的数据形态

DTO 的意义可以先理解成：

> 数据库里的对象，不一定应该原样传给前端。

项目中很重要的几个 DTO：

- `LoginFormDTO`：接登录参数
- `UserDTO`：保存 / 返回精简后的用户信息
- `Result`：统一接口返回结果
- `ScrollResult`：Feed 滚动分页结果

尤其注意：

```text
User（数据库实体）
     ↓ 转换
UserDTO（业务 / 登录态需要的数据）
```

这是非常常见的 Java Web 项目写法。

---

## 7. `interceptor/`：请求进入 Controller 前的处理

项目里有两个非常值得学习的拦截器：

```text
RefreshTokenInterceptor
LoginInterceptor
```

先理解职责分工：

### RefreshTokenInterceptor

负责：

```text
请求携带 token
→ 去 Redis 找用户
→ 找到后恢复 UserDTO
→ 放到 UserHolder(ThreadLocal)
→ 刷新 Redis 登录有效期
```

### LoginInterceptor

负责：

```text
当前请求是否必须登录？
→ UserHolder 中有没有用户
→ 没有则拦截
```

这两个不要混成“都是登录拦截器”。

一个偏向：**恢复登录状态**。

一个偏向：**权限校验**。

---

## 8. `config/`：框架级配置

当前源码里主要有：

```text
MvcConfig.java
MybatisConfig.java
RedissonConfig.java
WebExceptionAdvice.java
```

学习时重点关注：

### `MvcConfig`

用来注册拦截器等 MVC 配置。

### `RedissonConfig`

创建 Redisson 相关 Bean，是后面学习分布式锁的重要入口。

### `MybatisConfig`

MyBatis-Plus 相关配置。

### `WebExceptionAdvice`

统一异常处理。

---

## 9. `utils/`：项目基础工具

这里不要一开始全部读。

按模块需要再看。

特别值得认识：

```text
RedisConstants
SystemConstants
RegexUtils
UserHolder
RedisData
CacheClient
SimpleRedisLock
RedisIdWorker
```

它们分别会在登录、缓存、分布式锁和订单 ID 等模块中出现。

---

## 10. 第一遍建议阅读顺序

第一次打开项目，只按这个顺序：

```text
HmDianPingApplication
↓
UserController
↓
IUserService
↓
UserServiceImpl
↓
RefreshTokenInterceptor
↓
LoginInterceptor
↓
MvcConfig
↓
UserHolder
↓
RedisConstants
```

这一圈完成之后，你应该能回答：

1. 请求从哪里进入？
2. Controller 为什么不写大量业务逻辑？
3. Service 和 Mapper 分别负责什么？
4. Redis 登录状态是怎么重新恢复成“当前用户”的？
5. ThreadLocal 在这里为什么出现？

如果这五个问题能自己讲清楚，再进入商户缓存模块。
