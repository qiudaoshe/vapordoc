# Routing

路由是查找传入请求的适当响应的过程.

## 创建路由

Vapor 中的默认路由是 `EngineRouter`, 你可以通过实现路由器协议的路由来实现自定义路由.

```swift
let router = try EngineRouter.default()
```
这通常在 [`configure.swift`](structure.md#configureswift) 文件中实现.

## 注册路由

想象一下当有人访问 `GET /users` 的时候想要返回用户列表, 抛开授权, 路由可能会是这样:

```swift
router.get("users") { req in
    return // fetch the users
}
```
Vapor 中, 路由通常由 `.get`, `.put`, `.post`, `.patch` 和 `.delete` 实现. 你可以用 `/` 或者以逗号分隔的字符串来表示路径, 从可读性考虑我们推荐使用逗号分隔.

```swift
router.get("path", "to", "something") { ... }
```

## 路由

添加路由的最佳位置是 [`routes.swift`](structure.md#routesswift) 文件. 使用 `routes(_ router: Router)` 方法提供的路由器参数来注册路由.

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

有些时候你可能希望路由路径的某一部分是动态的, 比如你想要根据传入的 ID 来获取数据: `GET /users/:id`

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
