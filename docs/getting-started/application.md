# Application

每个 Vapor 工程都有一个 `Application(应用)`, 在 boot 期间通过 `Application ` 来运行服务器和创建服务.

在 [`boot.swift`](structure.md#bootswift) 中可以访问 `Application `.

```swift
import Vapor

public func boot(_ app: Application) throws {
    // your code here
}
```

与其他网络框架不同, Vapor 不支持静态的访问 Application. 如果在其他类或者结构体中需要访问, 你需要传递一个方法或者初始程序.

!!! info 
    避免对变量的静态访问有助于 Vapor 的性能提升: 阻止线程锁和信号量.


## Services

Application 的主方法是用来 boot 服务的.

```swift
try app.run()
```

Application 同时也是一个容器, 可以在 boot 的时候用来创建需要的服务.

!!! warning
    不要在路由的闭包中直接使用 Application 或者由它创建的服务, 使用 `Request` 来创建服务.

```swift
let client = try app.make(Client.self)
let res = try client.get("http://vapor.codes").wait()
print(res) // Response
```

!!! tip
    在这里可以使用 `,wait()` 来代替 `.map` 或者 `.flatMap`, 因为当前环境并不是在路由的闭包当中.

了解更多, 点击 [入门 &rarr; Services](services.md)
