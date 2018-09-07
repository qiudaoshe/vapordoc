# Routing

路由的作用是为即将到来的请求查找适合的回应.

## 创建路由

Vapor 中的默认路由是 `EngineRouter`, 通过遵守 `Router` 协议就可以创建自定义路由.

```swift
let router = try EngineRouter.default()
```
路由一般在 [`configure.swift`](structure.md#configureswift) 中定义.

## 注册路由

想象一下, 在有人访问 `GET /users` 的时候返回用户列表, 在不考虑权限的情况下, 路由可能会是这样:

```swift
router.get("users") { req in
    return // fetch the users
}
```
Vapor 中, 路由一般以 `.get`, `.put`, `.post`, `.patch` 和 `.delete` 声明. 你可以用 `/` 或者以逗号分隔的字符串来表示路径, 从可读性考虑我们推荐使用逗号分隔.

```swift
router.get("path", "to", "something") { ... }
```

## 路由

添加路由的最好的地方是在 [`routes.swift`](structure.md#routesswift) 文件中. 使用作为参数传递的 router 来注册你自己的路由.

```swift
import Vapor

public func routes(_ router: Router) throws {
    // Basic "Hello, world!" example
    router.get("hello") { req in
        return "Hello, world!"
    }
  
    /// ...
}
```
查看 [入门 &rarr; Content](content.md) 学习路由的闭包可以返回什么值.

## 参数

有些时候, 你想要你的路径一部分是动态的, 比如你想要根据传入的 ID 来获取数据: `GET /users/:id`

```swift
router.get("users", Int.parameter) { req -> String in
    let id = try req.parameter(Int.self)
    return "requested id #\(id)"
}
```
不传递字符串, 而是传递你希望的参数 _类型_, 在这个例子中, `User` 有 `Int` 类型的 ID.

>tip

>你也可以定义自己的 [自定义参数类型](../routing/parameters.md)

## 注册路由之后

在注册路由之后, 你需要将路由注册为一个 [入门 &rarr; 服务](services.md)
