# 黑马点评源码阅读索引

> 目标：以后打开源码时，不需要在几十个文件里乱翻。
>
> 先按“学习阶段 → 关键文件 → 关注点”来定位。

## 阶段 0：只认识项目骨架

先看：

```text
src/main/java/com/hmdp/HmDianPingApplication.java
src/main/java/com/hmdp/controller/UserController.java
src/main/java/com/hmdp/service/IUserService.java
src/main/java/com/hmdp/service/impl/UserServiceImpl.java
```

这一阶段只回答：

- Spring Boot 从哪里启动？
- Controller 怎么把请求转给 Service？
- Service 接口和 ServiceImpl 什么关系？
- MyBatis-Plus 的 `ServiceImpl<UserMapper, User>` 是什么角色？

不要急着研究 Redis 细节。

---

## 阶段 1：登录模块

关键文件：

```text
controller/UserController.java
service/IUserService.java
service/impl/UserServiceImpl.java
interceptor/RefreshTokenInterceptor.java
interceptor/LoginInterceptor.java
config/MvcConfig.java
utils/UserHolder.java
utils/RedisConstants.java
utils/RegexUtils.java
dto/LoginFormDTO.java
dto/UserDTO.java
entity/User.java
mapper/UserMapper.java
```

重点方法：

```text
UserController.sendCode
UserController.login
UserController.me
UserServiceImpl.sendCode
UserServiceImpl.login
```

重点 Java / Spring 写法：

- `@RestController`
- `@RequestMapping`
- `@PostMapping`
- `@RequestParam`
- `@RequestBody`
- `@Resource`
- Service 接口 + 实现类
- `LambdaQueryWrapper`
- `BeanUtil.copyProperties`
- `BeanUtil.beanToMap`

重点 Redis 写法：

```text
opsForValue().set(...)
opsForValue().get(...)
opsForHash().putAll(...)
expire(...)
```

学完必须能画出：

```text
手机号验证码
→ Redis
→ 登录校验
→ MySQL 用户
→ token
→ Redis Hash 用户态
→ Header token
→ Interceptor
→ UserHolder
```

---

## 阶段 2：商户缓存

关键文件：

```text
controller/ShopController.java
service/IShopService.java
service/impl/ShopServiceImpl.java
entity/Shop.java
mapper/ShopMapper.java
utils/RedisConstants.java
utils/CacheClient.java
utils/RedisData.java
```

先找：

```text
ShopController 中根据 id 查询商户的方法
```

再一路进入 `ShopServiceImpl`。

重点不是一次性看懂整个大文件，而是区分几种查询版本：

```text
直接查数据库
→ Redis 普通缓存
→ 解决穿透
→ 互斥锁解决击穿
→ 逻辑过期解决击穿
```

重点 Java / Redis 写法：

- JSON 序列化 / 反序列化
- Redis String
- TTL
- `setIfAbsent`
- 线程池异步重建缓存
- 泛型工具类设计（CacheClient）

---

## 阶段 3：秒杀基础

关键文件：

```text
controller/VoucherController.java
controller/VoucherOrderController.java
service/impl/VoucherServiceImpl.java
service/impl/SeckillVoucherServiceImpl.java
service/impl/VoucherOrderServiceImpl.java
entity/Voucher.java
entity/SeckillVoucher.java
entity/VoucherOrder.java
mapper/VoucherOrderMapper.java
```

先理解数据库版：

```text
秒杀时间
→ 库存
→ 扣减
→ 创建订单
```

再研究问题：

- 为什么超卖？
- 为什么要乐观锁？
- 为什么一人一单会有并发问题？
- 为什么 `synchronized` 在多实例下不够？

---

## 阶段 4：分布式锁

关键文件：

```text
utils/ILock.java
utils/SimpleRedisLock.java
config/RedissonConfig.java
service/impl/VoucherOrderServiceImpl.java
```

理解顺序：

```text
JVM synchronized
↓
Redis SETNX
↓
锁 TTL
↓
锁误删
↓
UUID / 线程标识
↓
Lua 解锁
↓
Redisson
```

重点不要只记 `tryLock()`。

必须知道每一步是在修前一步的哪个漏洞。

---

## 阶段 5：Lua + Redis Stream 异步秒杀

关键文件：

```text
service/impl/VoucherOrderServiceImpl.java
resources/seckill.lua
utils/RedisIdWorker.java
```

重点追踪：

```text
seckillVoucher(...)
```

看它如何逐渐从同步数据库逻辑，变成：

```text
Lua 原子校验
→ Redis 预扣库存
→ Stream 入队
→ 后台消费者
→ 数据库真正下单
```

重点概念：

- Lua 原子执行
- Stream Consumer Group
- Pending List
- 消费确认
- 异常后重试
- 最终一致性

---

## 阶段 6：探店博客、点赞、排行榜

关键文件：

```text
controller/BlogController.java
service/impl/BlogServiceImpl.java
entity/Blog.java
mapper/BlogMapper.java
```

重点 Redis：

```text
Sorted Set
```

重点业务：

- 点赞 / 取消点赞
- 判断当前用户是否点赞
- 最近点赞用户
- 点赞时间排序

---

## 阶段 7：关注与 Feed 流

关键文件：

```text
controller/FollowController.java
service/impl/FollowServiceImpl.java
service/impl/BlogServiceImpl.java
entity/Follow.java
```

重点：

```text
Set
Sorted Set
推模式
滚动分页
```

这部分建议先画数据流，不要直接扎进分页代码。

---

## 阶段 8：附近商户 GEO

关键文件：

```text
controller/ShopController.java
service/impl/ShopServiceImpl.java
```

重点：

- GEO 数据如何预写入 Redis
- 经纬度查询
- 距离排序
- Redis 查出 id 后如何恢复完整 Shop 数据

---

## 阶段 9：签到 BitMap

关键文件：

```text
controller/UserController.java
service/impl/UserServiceImpl.java
```

重点方法：

```text
sign()
signCount()
```

重点 Redis API：

```text
setBit
bitField
```

重点算法：

```text
从二进制最低位开始统计连续 1
```

---

## 阶段 10：UV HyperLogLog

这部分代码量通常不大。

学习目标不要定成“会背命令”，而是回答：

```text
为什么不直接 Set 存所有 userId？
```

关键权衡：

```text
精确去重计数
vs
极低内存 + 近似计数
```

---

# 推荐第一轮只打开的 10 个文件

如果现在刚开始学，先控制在下面 10 个：

```text
1. HmDianPingApplication.java
2. UserController.java
3. IUserService.java
4. UserServiceImpl.java
5. LoginFormDTO.java
6. UserDTO.java
7. User.java
8. RefreshTokenInterceptor.java
9. LoginInterceptor.java
10. MvcConfig.java
```

其他文件先按需打开。

这套项目最容易犯的学习错误就是：

> 一次把项目树所有文件都看一遍，结果每个文件都“见过”，但没有一条完整调用链真正讲得清楚。
