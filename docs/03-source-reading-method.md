# 黑马点评源码阅读方法

## 1. 不要从第一行一路读到最后一行

项目源码不是教材章节。

更稳定的方法是以“业务请求”为单位阅读。

例如：

```text
用户点击登录
→ 请求哪个接口？
→ 哪个 Controller 接收？
→ 调哪个 Service？
→ Redis 写了什么？
→ 最后返回什么？
```

## 2. 一条调用链的固定阅读模板

每次都按下面六个问题：

1. **入口是谁？**
2. **输入是什么？**
3. **核心业务判断在哪里？**
4. **数据库 / Redis 在哪里读写？**
5. **输出是什么？**
6. **异常或边界情况怎么处理？**

## 3. 第一次见到 Java 写法时怎么学

不要马上离开项目去学一整章 Java。

只记录：

- 这段语法叫什么
- 在这里解决什么问题
- 最小写法是什么
- 后面是否反复出现

如果一个写法在项目里反复出现，它才升级为“需要熟练掌握”。

## 4. 高频写法池

后续重点观察：

- Spring MVC Controller
- 注解参数绑定
- Service / ServiceImpl
- MyBatis-Plus CRUD / LambdaQuery
- BeanUtil
- DTO / Entity / VO
- StringRedisTemplate
- Lambda
- Stream API
- Optional（若出现）
- Executor / 并发
- synchronized
- Lua 调用
- Redisson
- 拦截器
- ThreadLocal

## 5. 每个模块的最终输出

每完成一个模块，只沉淀三类东西：

### A. 一张调用链

```text
Controller
→ Service
→ Redis / DB
→ Result
```

### B. 3~5 个高频写法

不要一次记几十个 API。

### C. 1~3 个设计思想

例如：

> Redis 缓存不是为了“用了 Redis”，而是为了降低数据库读压力；但加缓存后又引入一致性和击穿等新问题。

## 6. 从“会认”到“会写”的训练

推荐四步：

```text
第一遍：看懂
第二遍：遮住部分代码补全
第三遍：只看方法名 / 注释自己写
第四遍：脱离原项目做一个缩小版功能
```

