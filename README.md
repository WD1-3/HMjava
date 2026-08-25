# HMjava

Java / Redis 项目学习仓库。目前第一条主线是 **黑马点评（hm-dianping）**。

本仓库的目标不是简单收藏教程，而是把项目学习过程沉淀成可持续迭代的源码学习资料：

- 找到可靠源码与课程入口
- 建立项目模块地图
- 按真实调用链阅读代码
- 从“会认”逐步过渡到“会写”
- 记录高频 Java / Spring / Redis 写法
- 记录每个设计背后的业务问题与解决思路

## 当前主线：黑马点评

黑马点评是黑马程序员 Redis 实战课程中的核心项目，适合通过真实业务理解：

`Spring Boot + MyBatis-Plus + MySQL + Redis + Nginx`

主要模块：

```text
短信验证码登录
  ↓
Redis Session
  ↓
商户缓存
  ↓
缓存穿透 / 雪崩 / 击穿
  ↓
优惠券秒杀
  ↓
乐观锁 / 分布式锁
  ↓
Lua / Redisson
  ↓
Redis Stream 异步下单
  ↓
点赞 / 排行榜 / Feed
  ↓
GEO / BitMap / HyperLogLog
```

## 仓库导航

- [项目与资料总览](docs/00-overview.md)
- [源码与课程资料](docs/01-resources.md)
- [学习路线](docs/02-learning-roadmap.md)
- [源码阅读方法](docs/03-source-reading-method.md)
- [核心模块地图](docs/04-module-map.md)
- [环境启动与已知坑](docs/05-setup-and-pitfalls.md)
- [学习 TODO](progress/TODO.md)

## 学习原则

不要先背大量 API 再开始项目。

统一采用：

```text
会认代码
  ↓
看懂写法
  ↓
看懂调用链
  ↓
自己补代码
  ↓
能独立写
```

每学一个模块，都优先回答两个问题：

1. **这个模块解决什么真实业务问题？**
2. **代码为什么要这样设计，而不是只记 API？**

