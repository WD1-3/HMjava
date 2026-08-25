# 黑马点评源码与课程资料

## 1. 推荐主源码仓库

### GitHub 镜像

https://github.com/cs001020/hmdp

推荐原因：

- 有完整项目代码
- 有 `hmdp.sql`
- 有 `init` 初始版本
- 有 `master` 完整版本
- 适合一边自己写，一边对照答案

### 推荐使用方式

```bash
git clone https://github.com/cs001020/hmdp.git
cd hmdp
git checkout init
```

- `init`：主要练习分支
- `master`：答案 / 对照分支

学习时优先写 `init`，不要从头到尾只阅读 `master`。

## 2. Gitee 参考源码

后端：

https://gitee.com/huyi612/hm-dianping

前端：

https://gitee.com/huyi612/hmdp-web

## 3. 原课程

### Bilibili

黑马程序员 Redis 入门到实战 / 黑马点评：

https://www.bilibili.com/video/BV1cr4y1671t

黑马点评实战内容大约从课程中段开始。

### 黑马官方课程页

https://yun.itheima.com/course/994.html

课程覆盖 Redis 基础、共享 Session、缓存、分布式锁、消息队列、秒杀、集群及底层原理等。

## 4. 文字版学习笔记

https://cyborg2077.github.io/2022/10/22/RedisPractice/

适合作为视频之外的快速检索资料，覆盖：

- 短信登录
- 缓存问题
- 秒杀
- Lua
- 分布式锁
- 消息队列
- Feed
- GEO
- BitMap
- HyperLogLog

## 5. 资料使用原则

优先级建议：

```text
真实源码
> 自己动手
> 原课程
> 对照 master
> 第三方笔记
```

第三方笔记适合查漏补缺，不应替代自己走代码调用链。

