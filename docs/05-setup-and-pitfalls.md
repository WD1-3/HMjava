# 环境启动与已知问题

## 1. 推荐从 init 分支开始

不要为了“能直接跑完整功能”就只用 `master`。

`init` 更适合学习，因为很多核心实现需要自己补。

```bash
git clone https://github.com/cs001020/hmdp.git
cd hmdp
git checkout init
```

## 2. 数据库

仓库中可找到：

```text
hmdp.sql
```

先导入 MySQL，再检查 Spring Boot 数据源配置。

## 3. Redis

运行前确认：

- Redis 已启动
- host / port 正确
- 若配置密码，应用配置一致

## 4. Nginx / 前端

部分项目版本会将前端资源与 Nginx 配置放在项目资料中。

第一次学习的目标是确保：

```text
浏览器
→ 前端 / Nginx
→ Spring Boot API
→ MySQL / Redis
```

链路能通。

## 5. 完整版可能出现 Redis Stream 报错

直接运行某些 `master` 完整版本时，可能看到类似：

```text
NOGROUP No such key 'stream.orders'
```

原因通常是代码已经开始消费 Redis Stream，但对应 Stream / Consumer Group 尚未初始化。

这也是学习时建议从 `init` 分支开始的原因之一：不要让后续功能的运行依赖干扰前期学习。

## 6. 排错原则

按层排查：

```text
前端是否能访问
→ API 是否命中 Controller
→ Spring Boot 是否报错
→ MySQL 是否正常
→ Redis 是否正常
→ 当前分支是否与课程阶段一致
```

不要一次同时修改多个配置，否则很难确定真正原因。

