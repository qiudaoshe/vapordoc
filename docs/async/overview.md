# Async Overview

你可能已经注意到 Vapor 的某些 API 需要或者返回通用类型 `Future`, 如果这是你第一次接触 future (期货), 你可能会感到困惑, 但是别担心, 在 Vapor 中使用 future 很简单.

promise(承诺) 和 future(期货) 是相关的, 但是类型不同. promise 是用来 _创建_ future. 大多数情况, 你会使用 Vapor 的 API 所返回的 future, 而且不用担心创建 promise.

|类型     |描述                                          |可更改性|函数                                         |
|---------|-----------------------------------------------------|----------|----------------------------------------------------|
|`Future` |引用可能尚不可用的对象.|只读 |`.map(to:_:)` `.flatMap(to:_:)` `do(_:)` `catch(_:)`|
|`Promise`                                                     |承诺以异步方式提供一些对象.    |可读/写|`succeed(_:)` `fail(_:)`                            |

future 是基于回调的异步 API 的替代方案. 普通闭包不能链接和转换, 而 future 可以, 这让它们异常强大.

## Transforming

就像 Swift 中的可选类型, future 可以被 mapped(映射) 和 flat-mapped(平面映射). 这些是你可以在 future 上执行的的最常见操作.

|函数   |函数签名 |描述|
|---------|------------------|-----|
|`map`    |`to: U.Type, _: (T) -> U`        |将 _future_ 值映射为其他值.
|`flatMap`|`to: U.Type, _: (T) -> Future<U>`|将一个 _future_ 值映射为另一个 _future_ 值.|
|`transform`         |`to: U`                  |将 _future_ 值映射为已有值.|

如果你查看 `Optional<T>` 和 `Array<T>` 的方法 `map` 和 `flatMap` 的函数签名的话, 你会发现他们和 `Future<T>` 上的 `map` 和 `flatMap` 非常相似.

### Map

`.map(to:_:)` 函数允许你将 future 的值转化为另一个值. 因为 future 值可能还不可用(它可能是异步任务的结果), 我们必须提供一个闭包来接受该值.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

/// Map the future string to an integer
let futureInt = futureString.map(to: Int.self) { string in
    print(string) // The actual String
    return Int(string) ?? 0
}

/// We now have a future integer
print(futureInt) // Future<Int>
```

### Flat Map

`.flatMap(to:_:)` 函数允许你将一个 future 值转化为另一个 future 值. 它之所以被叫做 "平面", 是因为它可以让你避免创建嵌套的 future (例如, `Future<Future<T>>`). 换句话说, 它帮你朱保持 future 平衡.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

/// Assume we have created an HTTP client
let client: Client = ... 

/// Flat-map the future string to a future response
let futureResponse = futureString.flatMap(to: Response.self) { string in
    return client.get(string) // Future<Response>
}

/// We now have a future response
print(futureResponse) // Future<Response>
```

>info
>
>如果在上面示例代码中使用 `.map(to:_:)`, 我们就会获得一个 `Future<Future<Response>>`, 呀!

### Transform

`.transform(_:)` 函数允许你修改一个 future 的值, 而忽视现有的值. 这对于转换 `Future<Void>` 的结果非常有用, 其中 future 的实际值并不重要.

>tip
>
>`Future<Void>`, 有时称之为信号, 是一个 furue, 其唯一目的是通知你某些异步操作的完成或者失败.

```swift
/// Assume we get a void future back from some API
let userDidSave: Future<Void> = ...

/// Transform the void future to an HTTP status
let futureStatus = userDidSave.transform(to: HTTPStatus.ok)
print(futureStatus) // Future<HTTPStatus>
```   

尽管已经为 `transform` 提供了一个已经可用的值, 它仍然是 _转化_. 在所有之前的 future 完成 (或失败) 之前, future 不会完成.

### Chaining

关于 future 中的转化的重要的部分就是他们可以链接. 这允许你轻松地表达许多转换和子任务.

让我们修改上面的示例代码, 来看看我们利用链接.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

/// Assume we have created an HTTP client
let client: Client = ... 

/// Transform the string to a url, then to a response
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

在初始调用 `map` 后, 有一个临时 `Future<URL>` 被创建. 然后这个 future 立即被平面映射为 `Future<Response>`.

>tip
>
>你可以在 map 和 flat-map 闭包内部 `抛出` 错误. 这会导致 future 失败并且抛出错误.
    
## Future

让我们看看 `Future<T>` 中其他一些不常用的函数.

### Do / Catch

与 Swift 中的 `do` / `catch` 语法相似, future 也有 `do` 和 `catch` 函数来等待 future 结果.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

futureString.do { string in
    print(string) // The actual String
}.catch { error in
    print(error) // A Swift Error
}
```

>info
>
> `.do` 和 `.catch` 是成对出现的. 如果你忘记 `.catch`, 编译器会警告你未使用的结果. 不要忘记错误处理.

### Always

你可以使用 `always` 添加一个在 future 成功或失败执行的回调.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

futureString.always {
    print("The future is complete!")
}
```

>note
>
>你可以根据需要在 future 上添加任意多的回调.
    
### Wait

你可以使用 `.wait()` 来异步地等待 future 完成. 因为 future 可能会失败, 这个函数可以抛出错误.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

/// Block until the string is ready
let string = try futureString.wait()
print(string) /// String
```

>warning
>
>不要在路由闭包或者控制器中使用这个方法. 阅读 [Blocking](#blocking) 章节了解更多,
  
## Promise

大多数情况下, 你将通过调用 Vapor API 来改变返回的 future. 但是, 在有些时候你可能需要创建你自己的 promise.

要创建 promise, 你需要访问 `EventLoop`. Vapor 中所有的容器都有一个 `eventLoop` 属性供你使用. 最常见的是当前的 `Request`.

```swift
/// Create a new promise for some string
let promiseString = req.eventLoop.newPromise(String.self)
print(promiseString) // Promise<String>
print(promiseString.futureResult) // Future<String>

/// Completes the associated future
promiseString.succeed(result: "Hello")

/// Fails the associated future
promiseString.fail(error: ...)
```

>info
>
>promise 只能被执行一次. 任何后续的执行都会被忽略.
    
### Thread Safety

Promises can be completed (`succeed(result:)` / `fail(error:)`) from any thread. This is why promises require an event-loop to be initialized. Promises ensure that the completion action gets returned to its event-loop for execution.

## Event Loop

当你的应用程序启动的时候, 它通常会为它运行的每个 CPU 内核创建一个事件循环. 每个事件循环只有一个线程. 如果你熟悉 Node.js 的事件循环, 它与 Vapor 中的非常相似. 唯一的区别是 Vapor 可以在一个进程中运行多个时间循环, 因为 Swift 支持多线程.

每当一个客户端与你的服务器建立连接时, 它会分配给其中一个事件循环. 从那时起, 所有服务器和那个客户端之间的通信都会发生在同一个事件循环上(并与这个事件循环的线程建立联系).

事件循环负责跟踪每个建立连接的客户端的状态. 如果有来自客户端的请求等待读取, 事件循环触发读取数据的通知. 一旦整个请求被读取, 任何等待这条请求数据的 future 都会完成.

### Worker

有权访问事件循环的东西叫做 `Worker`. 每一个容器都有一个 worker.

最常用的用来与 Vapor 交互的容器有:

- `Application`
- `Request`
- `Response`

你可以通过这些容器的 `.eventLoop` 属性访问事件循环.

```swift
print(app.eventLoop) // EventLoop
```

Vapor 中有许多函数需要传递当前 worker. 通常都会被标记为 `on: Worker`. 如果你在一个路由闭包或控制器中, 传递当前 `Request` 或 `Response`. 如果在启动应用程序时需要 worker, 使用 `Application`.

### Blocking

请严格遵守:

>danger
>
>永远不要在事件循环上进行阻塞调用.
    
比如 `libc.sleep(_:)` 就是一个阻塞调用的例子.

```swift
router.get("hello") { req in
    /// Puts the event loop's thread to sleep.
    sleep(5)
    
    /// Returns a simple string once the thread re-awakens.
    return "Hello, world!"
}
```

`sleep(_:)` 根据提供的秒数阻塞当前线程的指令. 如果你直接在事件循环上执行阻塞操作, 事件循环在阻塞操作持续时间内不会响应任何分配给它的客户端. 换句话说, 如果你在事件循环中执行 `sleep(5)`, 那么连接到该事件的其他所有客户端 (可能数百或数千) 将被延时至少 5 秒.

确保在后台执行阻塞操作. 在工作完成的时候使用 promise 以非阻塞方式通知事件循环.

```swift
router.get("hello") { req in
    /// Create a new void promise
    let promise = req.eventLoop.newPromise(Void.self)
    
    /// Dispatch some work to happen on a background thread
    DispatchQueue.global() {
        /// Puts the background thread to sleep
        /// This will not affect any of the event loops
        sleep(5)
        
        /// When the "blocking work" has completed,
        /// complete the promise and its associated future.
        promise.succeed()
    }
    
    /// Wait for the future to be completed, 
    /// then transform the result to a simple String
    return promise.futureResult.transform(to: "Hello, world!")
}
```
    
并非所有的阻塞操作和 `sleep(_:)` 一样明细. 如果你担心你的行为会导致阻塞, 请仔细研究函数或询问别人. 如果函数要执行磁盘或网络 IO 操作并使用同步 API, 都可能导致阻塞.

>info
>
>如果执行阻塞操作是你应用程序的核心功能, 你应考虑使用 `BlockingIOThreadPool(阻塞 IO 线程池)` 来控制执行阻塞操作而创建的线程数.  这会帮你在执行阻塞操作的同时避免 CPU 时间的短缺.

    
