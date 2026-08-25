# Java 代码阅读能力：在黑马点评里从“看不懂”练到“有代码语感”

> 这条训练线服务于 P（原理）和 C（手搓）两条主线。
>
> 目标不是先把 Java 教材重新学一遍，而是让你在真实项目中逐步获得：**拆代码、读代码、认惯用写法、预测代码、最后自然写出 Java Web 代码**的能力。

---

# 1. 新手看 Java 项目为什么会觉得“每一行都认识，但连起来看不懂”

通常不是因为每个语法点都不会，而是同时遇到了四层信息：

```text
Java 语言本身
+
Spring / MyBatis-Plus / Redis 框架写法
+
项目自己的类和约定
+
业务逻辑
```

例如看到一个登录方法，新手容易同时卡在：

- `@Override` 是什么？
- `Result` 是什么？
- `LoginFormDTO` 是什么？
- `baseMapper` 从哪来的？
- LambdaQueryWrapper 为什么这么写？
- `opsForValue()` 为什么又返回一个对象？
- `TimeUnit.MINUTES` 是什么？
- 为什么 User 要先转 UserDTO？

如果每一处都立即跳出去系统学一章，主业务链会被切碎。

因此 HMjava 采用：

> **先按代码块读懂主干，再把反复出现的写法升级成 Java 高频能力。**

---

# 2. 看一段 Java 代码时，不要逐字符读

先按“块”切。

## 第一层：先看类是什么角色

先只看：

```text
包名
类名
注解
extends
implements
```

暂时不要进方法体。

问自己：

1. 这是 Controller / Service / Mapper / DTO / Entity / 工具类中的哪一种？
2. 它被 Spring 管理吗？
3. 它继承了谁？因此白拿了哪些能力？
4. 它实现了哪个接口？因此承诺提供哪些方法？

---

## 第二层：只看字段

例如：

```java
@Resource
private IUserService userService;
```

先翻译成人话：

> 这个 Controller 自己不创建 UserService，而是让 Spring 给它一个可以使用的用户服务对象。

先理解“关系”，再记 `@Resource`。

---

## 第三层：只看方法签名

例如简化成：

```java
@PostMapping("/login")
public Result login(@RequestBody LoginFormDTO form)
```

先读成一句中文：

> 收到 `/login` 的 POST 请求，把请求体转换成 LoginFormDTO，然后返回一个统一 Result。

做到这一层，就算方法体暂时没看，你也已经知道它的输入、输出和角色。

---

## 第四层：方法体按“业务块”读

不要一行一行翻译。

例如登录方法先划成：

```text
① 校验手机号
② 从 Redis 取验证码
③ 对比验证码
④ 查数据库用户
⑤ 必要时创建用户
⑥ 生成 token
⑦ 保存登录态到 Redis
⑧ 返回 token
```

然后再去看每一块 Java 怎么实现。

这一步非常关键：

> **先知道代码要干什么，再研究它是怎么写的。**

---

# 3. 每次读源码固定做“四层翻译”

以后我给你讲一段源码，会固定分成四层。

## L1：人话

一句话说这段代码在干什么。

## L2：Java 语法

解释这里用了什么语言特性：

- 类 / 接口
- 继承 / 实现
- 泛型
- Lambda
- 方法引用
- static
- final
- 集合
- 异常

## L3：框架惯用写法

解释它为什么在 Spring 项目里经常这样写：

- `@RestController`
- `@Service`
- `@Resource`
- DTO
- Mapper
- `StringRedisTemplate`
- MyBatis-Plus ServiceImpl

## L4：工程习惯

解释：

- 为什么不把逻辑全塞 Controller
- 为什么把重复逻辑抽成 private 方法
- 为什么用常量而不是魔法字符串
- 为什么返回 DTO 而不是 Entity
- 为什么接口和实现分开

最后才进入“你自己写”。

---

# 4. Java 阅读能力的阶段变化

## 阶段 J0：逐行看

典型表现：

- 每遇到一个注解就停
- 不清楚类型从哪来
- 方法调用链稍长就丢失
- 看见泛型、Lambda 就紧张

这是正常起点。

目标：先能把类、字段、方法、代码块分开。

---

## 阶段 J1：能按结构看

看到文件先找：

```text
这个类是谁
→ 它依赖谁
→ 它暴露哪些方法
→ 每个方法大概做几步
```

不再从 import 第一行一路读下去。

---

## 阶段 J2：认出高频模板

看到这些写法，不再逐字分析：

```text
@RestController
@RequestMapping
@PostMapping / @GetMapping
@Resource
@Service
@Override
Result.ok / Result.fail
DTO
StringRedisTemplate
getById / save / update
```

达到这一层后，阅读速度会明显提升。

---

## 阶段 J3：能跟类型和数据流

例如能跟住：

```text
LoginFormDTO
→ phone:String
→ User
→ UserDTO
→ Map<String,Object>
→ Redis Hash
→ token:String
```

不会只看“代码动作”，而是知道数据在不断变成什么类型。

---

## 阶段 J4：能预测下一段代码

读到：

```text
登录成功，接下来需要保存共享登录态
```

即使没看源码，也能猜：

```text
生成 token
→ User 转精简 DTO
→ Redis 保存 token 对应的用户信息
→ 设置 TTL
→ 返回 token
```

到这里，阅读已经从“被动理解”变成“主动预测”。

---

## 阶段 J5：具备 Java 项目语感

不是背某一份源码，而是看到需求会自然想到常见组织方式：

```text
接口入口放 Controller
业务判断放 Service
数据库走 Mapper / ServiceImpl
跨层传输用 DTO
常量集中管理
重复逻辑抽方法
依赖让 Spring 注入
```

J5 与 C4/C5 会逐渐汇合：开始自然写出相对符合 Java/Spring 习惯的代码。

---

# 5. “惯用写法”必须学，但不靠死背

以后把项目里的写法分三类。

## A 类：必须形成肌肉记忆

高频且几乎每个 Java Web 项目都会出现：

```text
类和方法基本结构
if / return
List / Map
new 对象
getter / setter
接口 implements
@Override
Spring Controller / Service 注解
依赖注入
DTO
常见 REST 参数绑定
```

这类最终应该自然写出来。

---

## B 类：熟悉即可，需要时能补出来

例如：

```text
MyBatis-Plus LambdaQueryWrapper
StringRedisTemplate 的各种 opsForXxx
BeanUtil
Redis BitField
Redisson API
Stream 消费组 API
```

不要求一开始逐字符默写。

要做到：

> 知道应该用谁、代码大概长什么样、借助 IDE 补全可以写出来。

---

## C 类：知道用途，随用随查

低频配置和复杂 API。

例如某些：

- 特殊框架配置参数
- 偶尔才用一次的命令
- 很长的 Builder 链

不要浪费精力硬背。

---

# 6. 每出现一种新 Java 写法，固定记录 7 件事

以后 Java 惯用写法笔记统一用下面模板：

```text
1. 写法叫什么？
2. 一句话人话解释？
3. 在黑马点评哪个地方出现？
4. 为什么这里需要它？
5. 最普通 / 展开的写法是什么？
6. 项目里为什么喜欢这种简洁写法？
7. 我要掌握到：会认 / 熟悉 / 会写 / 随用随查？
```

这样不会变成只有 API 名字的字典。

---

# 7. 特别训练：把“简写”还原成“笨写法”

新手经常不是逻辑不懂，而是 Java 老手写得太短。

例如遇到 Lambda / 链式调用，我们会先做：

```text
项目惯用写法
↓
拆成多行
↓
补全中间变量类型
↓
翻译成人话
↓
再合回原写法
```

这对下面内容尤其重要：

- Lambda
- 方法引用
- Builder
- Stream API
- MyBatis-Plus Wrapper
- RedisTemplate 链式 API

目标不是永远写“笨代码”，而是先理解简写到底省略了什么。

---

# 8. 特别训练：类型追踪

每遇到一条比较长的 Java 语句，都可以问：

```text
左边是什么类型？
右边返回什么类型？
中间每次调用后又变成什么？
```

例如概念上：

```text
stringRedisTemplate
  ↓ 调用 opsForValue()
ValueOperations<String,String>
  ↓ 调用 get(key)
String
```

一旦能追踪类型，很多“看起来很玄”的链式调用就会变简单。

---

# 9. 特别训练：接口 → 实现 → 调用

Java 项目经常不是直接：

```text
new UserServiceImpl()
```

而是：

```text
Controller 持有 IUserService
        ↓
Spring 注入 UserServiceImpl
        ↓
调用接口方法
```

所以以后遇到接口时不只解释“接口是什么”，还要练：

```text
谁声明接口？
谁实现它？
谁持有接口类型？
运行时真正调用谁？
```

这是看懂大型 Java 项目的核心能力之一。

---

# 10. 每个黑马点评模块增加一份“Java 阅读卡”

以后模块固定产出不再只有原理与手搓。

还要增加：

## Java 阅读卡

内容包括：

### 本模块第一次出现的语法

例如：

- 泛型
- Lambda
- static import

### 本模块框架惯用写法

例如：

- Controller 参数绑定
- ServiceImpl
- RedisTemplate

### 本模块工程习惯

例如：

- DTO 隔离 Entity
- 常量集中管理
- private helper method

### 本模块“先会认”的代码

3~8 个真正高频片段。

### 本模块必须手写的代码

只选最关键的几个，不要求把所有第三方 API 默写。

---

# 11. 第一阶段 Java 阅读训练：登录模块

登录模块优先建立下面这些代码语感：

```text
@RestController
@RequestMapping
@PostMapping
@GetMapping
@RequestParam
@RequestBody
@PathVariable
@Resource
@Service
@Override
interface / implements
extends ServiceImpl<Mapper,Entity>
Result
DTO
BeanUtil.copyProperties
LambdaQueryWrapper
StringRedisTemplate
Map<String,Object>
TimeUnit
private helper method
static 常量
ThreadLocal
HandlerInterceptor
```

不是一天背完。

随着 `/user/code` → `/user/login` → 拦截器这三条链逐个出现、逐个掌握。

---

# 12. 登录模块第一轮只要求什么

第一次看 `/user/code`，Java 只要求先认：

```text
@PostMapping
@RequestParam
@Resource
Service 调用
Result
StringRedisTemplate
if + return
TimeUnit.MINUTES
```

第二次到 `/user/login`，再增加：

```text
@RequestBody
DTO
LambdaQueryWrapper
泛型
BeanUtil
Map
UUID
private helper method
```

第三次看登录态恢复，再增加：

```text
Interceptor
ThreadLocal
static 方法
配置类注册
```

这样难度是逐步叠加的，而不是第一次打开项目就要求认识所有 Java 写法。

---

# 13. 最终希望形成的阅读习惯

看到一份新 Java 文件，不再问：

> “这一整页代码是什么意思？”

而是自动开始：

```text
这是什么层？
↓
这个类继承 / 实现谁？
↓
它依赖哪些对象？
↓
入口方法是什么？
↓
方法分几块业务逻辑？
↓
数据类型怎么变化？
↓
哪些是项目高频惯用写法？
↓
哪些细节暂时可以跳过？
```

这就是我们要培养的“会看 Java 项目”的能力。
