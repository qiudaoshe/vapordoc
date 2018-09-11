# Controllers

Controllers(控制器) 是组织代码的好方法, 控制器是接受请求并返回相应的方法集合.

放置控制器的好方法是 [Controllers](structure.md#controllers) 目录.

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

控制器的方法总是应该接收 `Request`, 并且返回 `ResponseEncodable`.

> note
>
>  [Futures](async.md) 的期望值是 `ResponseEncodable` (比如 `Future<String>`), 而 Future 本身也是 `ResponseEncodable`.

使用控制器时, 我们只要简单的初始化它, 然后将方法传递给路由.

```swift
let helloController = HelloController()
router.get("greet", use: helloController.greet)
```

## Using Services

若你想在你的控制器内部访问 [services](services.md), 只要将 `Request` 作为容器在路由闭包中创建服务, Vapor 负责缓存服务.

```swift
final class HelloController {
	func greet(_ req: Request) throws -> String {
		return try req.make(BCryptHasher.self).hash("hello")
	}
}
```
