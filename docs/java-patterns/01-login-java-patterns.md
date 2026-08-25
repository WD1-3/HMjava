# 登录模块 Java 惯用写法卡（第一批）

> 目标：不是背 API，而是让这些写法从“陌生字符”逐渐变成“看到就懂、需要时能写”。

---

# 1. `@RestController`

## 这是什么

Spring MVC 中用于声明 REST Controller 的注解。

## 人话

> 这个类负责接收 HTTP 请求，并把返回值作为接口响应返回。

## 为什么黑马点评这里这么写

`UserController` 对外暴露 `/user/...` 接口，因此它是请求入口层。

## 先掌握到什么程度

**必须熟悉。**

看到它就应该自动判断：

> “这是 Controller，我先去看 RequestMapping 和每个方法对应哪个接口。”

---

# 2. `@RequestMapping("/user")`

## 人话

给整个 Controller 加统一路径前缀。

例如：

```java
@RequestMapping("/user")
```

再配：

```java
@PostMapping("/login")
```

最终接口就是：

```text
POST /user/login
```

## 习惯

读 Controller 时，先看类上的路径，再看方法上的路径，不要只看方法注解。

---

# 3. `@PostMapping` / `@GetMapping`

## 人话

把 Java 方法映射成 HTTP 接口。

```java
@PostMapping("/login")
```

表示这个方法处理 POST `/login`。

```java
@GetMapping("/me")
```

表示这个方法处理 GET `/me`。

## 必须形成的代码语感

看到：

```java
@PostMapping("/login")
public Result login(...)
```

不要先盯着方法体。

先读成：

> “这里有个登录接口，它返回 Result。”

---

# 4. `@RequestParam`

例如：

```java
public Result sendCode(@RequestParam("phone") String phone)
```

## 人话

从请求参数中拿 `phone`，转成 Java 的 `String phone`。

可以先理解为：

```text
HTTP 参数
phone=138...
    ↓
Java
String phone
```

## 掌握目标

**会认 + 会写。**

---

# 5. `@RequestBody`

例如登录：

```java
public Result login(@RequestBody LoginFormDTO loginForm)
```

## 人话

前端传来一整个 JSON 对象，Spring 自动把它转换成一个 Java DTO。

概念上：

```json
{
  "phone": "138...",
  "code": "123456"
}
```

变成：

```java
LoginFormDTO loginForm
```

## 为什么不是传很多单独参数

登录数据天然属于一组数据，因此封装成 DTO 更容易维护。

---

# 6. DTO：为什么不是直接用 `User`

黑马点评会出现：

```text
LoginFormDTO
UserDTO
User
```

它们不是同一个角色。

## `User`

数据库实体。

## `LoginFormDTO`

前端提交登录信息时使用。

## `UserDTO`

业务层 / 登录态只需要的精简用户信息。

## Java 工程习惯

不要形成：

```text
“有 User 类了，所有地方都用 User 就行。”
```

更常见的工程写法是：

```text
数据库对象
≠
接口输入对象
≠
接口输出 / 登录上下文对象
```

---

# 7. `@Resource`

项目中常见：

```java
@Resource
private IUserService userService;
```

## 人话

> 我需要一个 IUserService，你 Spring 帮我把合适的对象放进来。

不是自己写：

```java
IUserService userService = new UserServiceImpl();
```

## 为什么 Java/Spring 项目习惯这么做

因为对象创建和依赖关系交给容器管理，类只关心“我需要什么”。

## 新手第一阶段

不需要先把 IoC / DI 所有理论啃完。

先形成直觉：

```text
@Resource
↓
这个字段通常不是自己 new 的
↓
Spring 注入
```

---

# 8. 接口类型持有实现对象

`UserController` 持有：

```java
private IUserService userService;
```

而真正实现业务的是：

```java
UserServiceImpl
```

它们关系类似：

```text
IUserService：规定“能做什么”
          ↑
UserServiceImpl：真正“怎么做”
```

Spring 最终把实现对象注入给接口类型变量。

## 阅读代码的重要习惯

看到：

```java
userService.login(...)
```

如果想知道实现，不要只在 Controller 里找。

要追：

```text
IUserService.login
       ↓
UserServiceImpl.login
```

---

# 9. `@Override`

例如：

```java
@Override
public Result login(...) {
```

## 人话

> 这个方法不是随便新写的，它是在实现父类 / 接口里已经规定的方法。

看到 `@Override`，可以主动追问：

> “它从哪里继承 / 实现来的？”

这会帮助你建立类之间的关系图。

---

# 10. `extends ServiceImpl<UserMapper, User>`

这是黑马点评里新手很容易被吓到的一段。

先拆开：

```java
ServiceImpl<UserMapper, User>
```

可以先理解为 MyBatis-Plus 提供的一个通用 Service 实现基类。

泛型告诉它：

```text
这个 Service 使用 UserMapper
处理 User 实体
```

于是 `UserServiceImpl` 可以直接获得很多已有能力。

例如项目里会直接出现：

```java
baseMapper
```

它不是在当前类里手写声明的，而是继承来的。

## 阅读技巧

看到一个变量“当前文件根本没定义”，不要立刻认为代码有问题。

检查：

```text
父类里有没有？
接口默认方法有没有？
Spring / Lombok 有没有生成？
```

---

# 11. 泛型 `<UserMapper, User>` 怎么先看懂

新手阶段不要先把泛型所有知识学完。

这里先读成人话：

```text
ServiceImpl<处理数据库的 Mapper 类型, 处理的数据实体类型>
```

也就是：

```java
ServiceImpl<UserMapper, User>
```

≈

> “这是为 User 这一类数据工作的 ServiceImpl，底层 Mapper 是 UserMapper。”

先会读，再慢慢补泛型原理。

---

# 12. `Result.ok()` / `Result.fail()`

这是项目自己的统一返回包装。

例如：

```java
return Result.ok();
```

或：

```java
return Result.fail("手机号格式错误");
```

## 人话

Controller / Service 不直接随意返回各种形状的数据，而是统一包装成 Result。

## 工程习惯

统一返回结构可以让前后端更容易约定：

```text
成功 / 失败
数据
错误信息
```

---

# 13. `if (...) return ...`：失败尽早返回

登录代码大量采用：

```java
if (条件不满足) {
    return Result.fail(...);
}
```

然后继续正常流程。

这种代码阅读起来通常比多层嵌套更直：

```text
先排除错误情况
↓
继续主流程
```

以后你自己写 Service，也可以优先考虑这种“guard clause / early return”思路。

---

# 14. `StringRedisTemplate`

例如：

```java
@Resource
private StringRedisTemplate stringRedisTemplate;
```

## 人话

Spring 提供的一个 Redis 操作工具对象，项目里大量 Redis String / Hash / BitMap 操作都从这里开始。

## 读链式调用的方法

不要一次看整条：

```java
stringRedisTemplate.opsForValue().set(...)
```

拆开：

```text
stringRedisTemplate
↓
opsForValue()
选择“按 String Value 的方式操作 Redis”
↓
set(...)
执行写入
```

以后看到：

```text
opsForHash()
opsForSet()
opsForZSet()
```

也可以用同样方式读。

---

# 15. `TimeUnit.MINUTES`

例如：

```java
..., LOGIN_CODE_TTL, TimeUnit.MINUTES
```

## 人话

告诉 API：前面的 TTL 数字按“分钟”理解。

这种 enum 常量写法比直接用神秘数字表达单位更清楚。

---

# 16. `private` helper method

项目中登录会把“根据手机号创建新用户”抽成：

```java
private User createUserWithPhone(String phone)
```

## 为什么不全部塞进 `login()`

因为 login 的主流程应该保持清晰：

```text
校验
→ 查验证码
→ 查用户
→ 必要时创建
→ 保存登录态
```

“创建用户的具体细节”是一个可以命名的小任务，因此抽出去。

## Java 工程习惯

当一个方法内部出现一块：

- 能用一句话命名
- 有独立意义
- 会让主流程变长

就值得考虑抽成 private 方法。

---

# 17. 第一轮真正需要手写熟练的只有这些

先不要背整个登录源码。

第一批要求逐渐自然写出：

```java
@RestController
@RequestMapping(...)
@PostMapping(...)
@GetMapping(...)
@Resource
@Service
@Override
```

以及这种基本结构：

```java
if (...) {
    return Result.fail(...);
}

return Result.ok(...);
```

和：

```java
stringRedisTemplate.opsForValue().set(...);
stringRedisTemplate.opsForValue().get(...);
```

其余复杂写法先达到“会认 + 会解释”。

---

# 18. 第一批自测

看到下面代码时：

```java
@PostMapping("/login")
public Result login(@RequestBody LoginFormDTO loginForm) {
    return userService.login(loginForm);
}
```

应该能够快速说出：

1. 这是 POST 接口。
2. 路径还要结合类上的 RequestMapping 才完整。
3. 请求体会被 Spring 转成 LoginFormDTO。
4. Controller 自己没写登录业务，而是交给 Service。
5. 返回统一 Result。

如果这五句仍需要逐字分析，说明这些写法目前处于“会认”阶段；后面通过反复出现让它们变成代码语感。
