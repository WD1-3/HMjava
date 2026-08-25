# 黑马点评核心模块地图

## 1. 用户与登录

业务问题：多实例部署下如何共享登录状态。

核心知识：

- Redis String / Hash
- token
- 拦截器
- ThreadLocal
- Session 共享

---

## 2. 商户缓存

业务问题：大量查询直接打数据库。

核心知识：

- Cache Aside
- TTL
- 缓存穿透
- 缓存雪崩
- 缓存击穿
- 互斥锁
- 逻辑过期

---

## 3. 秒杀优惠券

业务问题：高并发下库存和订单一致性。

核心知识：

- 乐观锁
- CAS 思想
- 一人一单
- 数据库事务
- Redis 原子操作

---

## 4. 分布式锁

业务问题：应用部署多个 JVM 后，本地锁失效。

核心知识：

- SETNX
- 锁超时
- 锁误删
- Lua
- Redisson
- 可重入
- WatchDog

---

## 5. 异步下单

业务问题：秒杀流量峰值直接同步写数据库代价高。

核心知识：

- Lua
- Redis Stream
- Consumer Group
- Pending List
- 异步任务

---

## 6. 点赞与排行榜

核心知识：

- Sorted Set
- 排名
- Top N

---

## 7. 关注与 Feed

核心知识：

- Set
- Sorted Set
- 推模式
- Feed 流
- 滚动分页

---

## 8. 附近商户

核心知识：

- GEO
- 距离排序

---

## 9. 签到

核心知识：

- BitMap
- 位运算
- 连续签到统计

---

## 10. UV

核心知识：

- HyperLogLog
- 近似去重计数
- 内存与精度权衡

