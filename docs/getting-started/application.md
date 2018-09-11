# Application

每个 Vapor 工程都有一个 `Application(应用程序)`, 你可以通过 `Application ` 运行服务器和创建任务启动时可能需要的服务.

访问 `Application` 的最佳位置是 [`boot.swift`](structure.md#bootswift) 文件.

```swift
import Vapor

public func boot(_ app: Application) throws {
    // your code here
}
```

与其他网络框架不同, Vapor 不支持静态的访问应用程序. 如果需要从另一个类或者结构体中需要访问它, 你需要通过方法或者初始器.

> info
>
> 避免对变量的静态访问有助于通过防止对线程锁和信号量的需要来提升 Vapor 的性能:


## Services

Application 的主方法是用来启动服务器的.

```swift
try app.run()
```

Application 同时也是一个容器, 可以用来创建应用程序启动所需的服务.

> warning
>
> 不要在路由闭包中直接使用 Application 或者从中创建的任何服务, 使用 `Request` 来创建服务.

```swift
let client = try app.make(Client.self)
let res = try client.get("http://vapor.codes").wait()
print(res) // Response
```

> tip
>
> 在这里可以使用 `,wait()` 来代替 `.map` 或者 `.flatMap`, 因为当前环境并不是在路由的闭包当中.

了解更多, 点击 [入门 &rarr; Services](services.md)
