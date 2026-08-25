# HMjava

Java / Redis 项目学习仓库。目前第一条主线是 **黑马点评（hm-dianping）**。

本仓库的目标不是简单收藏教程，而是把项目学习过程沉淀成可持续迭代的源码学习资料：

- 找到可靠源码与课程入口
- 建立项目模块地图
- 按真实调用链阅读代码
- 同时训练“原理理解”和“从零实现”
- 专门训练 Java 代码阅读能力和项目惯用写法
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

## 核心学习方式：两条主线 + 一条贯穿训练线

每个模块同时看三个能力：

```text
P = Principle：原理理解
C = Coding：实现 / 手搓能力
J = Java Literacy：Java 代码阅读与惯用写法能力
```

### P：为什么这么设计

目标从“听过”走到“换业务也能自己设计”。

### C：能不能自己写

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

### J：能不能越来越快地看懂 Java 项目

```text
J0 逐行看，容易被注解 / 泛型 / 链式调用卡住
 ↓
J1 能按“类 → 字段 → 方法 → 业务块”阅读
 ↓
J2 高频 Spring / Java 写法看到就认识
 ↓
J3 能稳定追踪类型、接口实现和数据流
 ↓
J4 看需求 / 上半段代码能预测后续常见实现
 ↓
J5 形成 Java Web 项目代码语感
```

J 不是第三门要单独学完的课程，而是贯穿 P/C 全程。

比如学登录模块时，同时学习：

```text
原理：为什么需要 Redis token 登录态
实现：怎么写 sendCode / login / interceptor
Java 阅读：@RequestBody、DTO、@Resource、interface/implements、泛型、ServiceImpl、ThreadLocal 等写法怎么读
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
- [Java 代码阅读能力训练](docs/10-java-code-literacy.md)

### Java 惯用写法卡

- [登录模块 Java 惯用写法（第一批）](docs/java-patterns/01-login-java-patterns.md)

### 学习进度

- [学习 TODO](progress/TODO.md)
- [P/C 双维能力矩阵](progress/MASTERY-MATRIX.md)

## 推荐当前学习入口

如果刚开始黑马点评，不要把仓库所有资料一起看。

先按：

```text
09-dual-track-learning-system
  ↓
10-java-code-literacy
  ↓
00-overview
  ↓
06-project-architecture
  ↓
07-core-request-flows（先只看登录前三条链）
  ↓
01-login-java-patterns（跟着源码逐个认识，不背）
```

第一套完整样板是“登录模块”：

```text
原理 P0 → P4
实现 C0 → C4
Java 阅读 J0 → J2/J3
```

先把登录真正练到：能讲、能看、能写，再进入商户缓存。

## 每个模块的固定训练循环

```text
先知道它解决什么问题
        ↓
读真实源码（会认）
        ↓
把陌生 Java 写法拆成人话 / 展开写法
        ↓
自己画原理 / 数据流
        ↓
重新读源码（熟悉 Java/Spring 惯用写法）
        ↓
遮住关键代码补全
        ↓
只给骨架重新实现
        ↓
只给业务需求，从零手搓
        ↓
换场景迁移
```

每学一个模块，都要回答三个问题：

1. **这个模块为什么这样设计？**
2. **这段 Java 为什么习惯这样写，我能顺畅读懂吗？**
3. **如果不给我原源码，我现在能写到哪一步？**

