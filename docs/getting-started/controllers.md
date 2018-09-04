# Controllers

Controllers(控制器) 可以用来组织代码, 控制器包含一系列接收请求和返回一个响应的方法.

Vapor 推荐将控制器放到 [Controllers](structure.md#controllers).

## Methods

一个典型的控制器是这样的:

```swift
import Vapor

final class HelloController {
	func greet(_ req: Request) throws -> String {
		return "Hello!"
	}
}
```

控制器的方法总是应该接收一个 `Request`, 并且返回 `ResponseEncodable ` 类型数据.

!!! note
    [Futures](async.md) 的期望值是 `ResponseEncodable` (比如 `Future<String>`), 而 Future 本身也是 `ResponseEncodable`.

使用控制器时, 我们只要简单的初始化它, 然后调用一个路由.

```swift
let helloController = HelloController()
router.get("greet", use: helloController.greet)
```

## Using Services

若你想在你的控制器内部访问 [services](services.md), 只要将路由闭包中的 `Request` 作为容器来创建服务, 服务由 Vapor 帮你管理.

```swift
final class HelloController {
	func greet(_ req: Request) throws -> String {
		return try req.make(BCryptHasher.self).hash("hello")
	}
}
```
