# Async

你可能已经注意到 Vapor 的某些 API 需要或者返回通用类型 `Future`, 如果这是你第一次接触 future (期货), 你可能会感到困惑, 但是别担心, 在 Vapor 中使用 future 很简单.

本指南为你提供使用异步的快速入门, 查看 [Async → Overview](../async/overview.md) 获取更多信息.

## Futures

因为 `Future` 是异步工作的, 我们需要使用闭包来与它们交互和使用它们的值, 就像 Swift 中的可选类型, future(期货) 可以被 map(映射) 或者 flatmap(平面映射).

### Map

`.map(to:_:)` 函数允许你将期货的值转化为另一个值, 一旦期货数据可用, 闭包将会调用.

```swift
/// 假定我们从某个 API 获得了 Future<String>
let futureString: Future<String> = ...

/// 将 future 解析为 Int
let futureInt = futureString.map(to: Int.self) { string in
    print(string) // The actual String
    return Int(string) ?? 0
}

/// 现在我们得到了一个 Int 值
print(futureInt) // Future<Int>
```

### Flat Map

`.flatMap(to:_:)` 函数允许你将一个期货值转换为另一个期货值, 之所以叫做 "平面"映射, 是因为它帮你避免创建了嵌套式的期货 (比如, `Future<Future<T>>`).

```swift
/// 假定我们从某个 API 获得了 Future<String>
let futureString: Future<String> = ...

/// 假定我们创建了一个 HTTP 客户端
let client: Client = ... 

/// 将 future 字符串映射为 future 响应
let futureResponse = futureString.flatMap(to: Response.self) { string in
    return client.get(string) // Future<Response>
}

/// 现在我们得到了一个 future 响应
print(futureResponse) // Future<Response>
```

>info
>
>如果在上面示例代码中使用 `.map(to:_:)`, 我们就会获得一个 `Future<Future<Response>>`, 呀!
    

### Chaining

future 的转换中, 最棒的事情就是它支持链接, 你可以轻松地表示多个转换或者子任务.

```swift
/// 假定我们从某个 API 获得了 Future<String>
let futureString: Future<String> = ...

/// 假定我们创建了一个 HTTP 客户端
let client: Client = ... 

/// 将字符串转变为 url, 然后转变为一个响应.
let futureResponse = futureString.map(to: URL.self) { string in
    guard let url = URL(string: string) else {
        throw Abort(.badRequest, reason: "Invalid URL string: \(string)")
    }
    return url
}.flatMap(to: Response.self) { url in
    return client.get(url)
}

print(futureResponse) // Future<Response>
```

在初始的映射创建了一个临时的 `Future<URL>`, 然后它立即被平面映射为一个 `Future<Response>`.

>tip
>
>你可以在 map 或者 flatmap 的闭包中 `throw` 错误, 这会导致 future 在抛出错误的时候失败.

## Worker

你也许看见过 Vapor 的函数带有 `on:Worker` 参数, 这些一般是异步执行的函数, 并且需要 `EventLoop` 的权限.

最常接触的 `Worker`:

- `Application`
- `Request`
- `Response`

```swift
/// 假定我们有一个 Request 和 ViewRenderer
let req: Request = ...
let view: ViewRenderer = ...

/// 将 Request 作为 worker 来渲染视图. 
/// 这保证了异步任务工作在正确的事件回路中.
/// 这个方法声明为:
/// func render(_: String, on: Worker)
view.render("home.html", on: req)
```