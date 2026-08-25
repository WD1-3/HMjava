# 黑马点评学习 TODO

## Phase 0：运行环境

- [ ] Clone `cs001020/hmdp`
- [ ] 切换 `init` 分支
- [ ] 导入 `hmdp.sql`
- [ ] 配置 MySQL
- [ ] 配置 Redis
- [ ] 启动 Spring Boot 后端
- [ ] 打开前端页面

## Phase 1：项目结构

- [ ] 找到 Controller 目录
- [ ] 找到 Service / ServiceImpl
- [ ] 找到 Mapper
- [ ] 理解 Entity / DTO / VO
- [ ] 找到配置文件
- [ ] 找到 Redis 工具相关代码
- [ ] 完整走通第一条请求链路

### 完成标准

- [ ] 能解释 Controller / Service / Mapper 各负责什么
- [ ] 能口述一次 HTTP 请求进入项目后的主要路径

## Phase 2：登录

- [ ] 短信验证码
- [ ] token 登录
- [ ] Redis Session
- [ ] 登录拦截器
- [ ] ThreadLocal
- [ ] 登录状态刷新

## Phase 3：缓存

- [ ] 商户缓存
- [ ] Cache Aside
- [ ] 缓存穿透
- [ ] 缓存雪崩
- [ ] 缓存击穿
- [ ] 互斥锁
- [ ] 逻辑过期

## Phase 4：秒杀

- [ ] 优惠券秒杀
- [ ] 库存超卖
- [ ] 乐观锁
- [ ] 一人一单
- [ ] 事务

## Phase 5：分布式锁

- [ ] SETNX
- [ ] 锁误删
- [ ] Lua 解锁
- [ ] Redisson
- [ ] WatchDog

## Phase 6：异步下单

- [ ] Lua 前置校验
- [ ] Redis Stream
- [ ] Consumer Group
- [ ] Pending List
- [ ] 异步订单处理

## Phase 7：社交功能

- [ ] 点赞
- [ ] 排行榜
- [ ] 关注
- [ ] 共同关注
- [ ] Feed 流
- [ ] 滚动分页

## Phase 8：高级数据结构

- [ ] GEO 附近商户
- [ ] BitMap 签到
- [ ] HyperLogLog UV

## 每个模块固定产出

- [ ] 一张调用链
- [ ] 3~5 个高频代码写法
- [ ] 1~3 个核心设计思想
- [ ] 至少一次 `init` 分支自行补代码

