# HMjava

Java / Redis 项目学习仓库。目前第一条主线是 **黑马点评（hm-dianping）**。

本仓库的目标不是简单收藏教程，而是把项目学习过程沉淀成可持续迭代的源码学习资料：

- 找到可靠源码与课程入口
- 建立项目模块地图
- 按真实调用链阅读代码
- 同时训练“原理理解”和“从零实现”
- 从“会认”逐步过渡到“熟悉、补全、手搓、迁移”
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

## 核心学习方式：双维推进

以后每一个模块都同时记录两个能力：

```text
P = Principle：原理理解
C = Coding：实现 / 手搓能力
```

例如：

```text
登录模块
原理：P3 / P4
实现：C2 / C4
```

其中实现能力按下面路线训练：

```text
C0 会认
 ↓
C1 熟悉
 ↓
C2 遮住关键代码能补
 ↓
C3 给骨架能写
 ↓
C4 只给需求，从零手搓
 ↓
C5 换业务仍能重新实现
```

所以“视频看完”不再代表“学会”。

## 仓库导航

### 基础资料

- [项目与资料总览](docs/00-overview.md)
- [源码与课程资料](docs/01-resources.md)
- [学习路线](docs/02-learning-roadmap.md)
- [源码阅读方法](docs/03-source-reading-method.md)
- [核心模块地图](docs/04-module-map.md)
- [环境启动与已知坑](docs/05-setup-and-pitfalls.md)

### 源码学习骨架

- [项目架构：各层分别在干什么](docs/06-project-architecture.md)
- [核心业务调用链索引](docs/07-core-request-flows.md)
- [源码阅读索引：每阶段该看哪些文件](docs/08-source-reading-index.md)
- [双维学习体系：原理 + 从零手搓](docs/09-dual-track-learning-system.md)

### 学习进度

- [学习 TODO](progress/TODO.md)
- [P/C 双维能力矩阵](progress/MASTERY-MATRIX.md)

## 推荐当前学习入口

如果刚开始黑马点评，不要把仓库所有资料一起看。

先按：

```text
09-dual-track-learning-system
  ↓
00-overview
  ↓
06-project-architecture
  ↓
07-core-request-flows（先只看登录前三条链）
  ↓
08-source-reading-index（只打开前 10 个文件）
```

第一套完整样板是“登录模块”：

```text
原理 P0 → P4
实现 C0 → C4
```

先把登录真正练到能讲、能写，再进入商户缓存。

## 每个模块的固定训练循环

```text
先知道它解决什么问题
        ↓
读真实源码（会认）
        ↓
自己画原理 / 数据流
        ↓
重新读源码（熟悉写法）
        ↓
遮住关键代码补全
        ↓
只给骨架重新实现
        ↓
只给业务需求，从零手搓
        ↓
换场景迁移
```

每学一个模块，都要回答两个问题：

1. **这个模块为什么这样设计？**
2. **如果不给我原源码，我现在能写到哪一步？**

