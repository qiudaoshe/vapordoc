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

The `.map(to:_:)` method allows you to transform the future's value to another value. Because the future's value may not be available yet (it may be the result of an asynchronous task) we must provide a closure to accept the value.

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

The `.flatMap(to:_:)` method allows you to transform the future's value to another future value. It gets the name "flat" map because it is what allows you to avoid creating nested futures (e.g., `Future<Future<T>>`). In other words, it helps you keep your generic futures flat.

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

!!! info
    If we instead used `.map(to:_:)` in the above example, we would have ended up with a `Future<Future<Response>>`. Yikes!
    
### Transform

The `.transform(_:)` method allows you to modify a future's value, ignoring the existing value. This is especially useful for transforming the results of `Future<Void>` where the actual value of the future is not important.

!!! tip
    `Future<Void>`, sometimes called a signal, is a future whose sole purpose is to notify you of completion or failure of some async operation.

```swift
/// Assume we get a void future back from some API
let userDidSave: Future<Void> = ...

/// Transform the void future to an HTTP status
let futureStatus = userDidSave.transform(to: HTTPStatus.ok)
print(futureStatus) // Future<HTTPStatus>
```   

Even though we have supplied an already-available value to `transform`, this is still a _transformation_. The future will not complete until all previous futures have completed (or failed).

### Chaining

The great part about transformations on futures is that they can be chained. This allows you to express many conversions and subtasks easily.

Let's modify the examples from above to see how we can take advantage of chaining.

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

After the initial call to map, there is a temporary `Future<URL>` created. This future is then immediately flat-mapped to a `Future<Response>`

!!! tip
    You can `throw` errors inside of map and flat-map closures. This will result in the future failing with the error thrown.
    
## Future

Let's take a look at some other, less commonly used methods on `Future<T>`.

### Do / Catch

Similar to Swift's `do` / `catch` syntax, futures have a `do` and `catch` method for awaiting the future's result.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

futureString.do { string in
    print(string) // The actual String
}.catch { error in
    print(error) // A Swift Error
}
```

!!! info
    `.do` and `.catch` work together. If you forget `.catch`, the compiler will warn you about an unused result. Don't forget to handle the error case!

### Always

You can use `always` to add a callback that will be executed whether the future succeeds or fails.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

futureString.always {
    print("The future is complete!")
}
```

!!! note
    You can add as many callbacks to a future as you want.
    
### Wait

You can use `.wait()` to synchronously wait for the future to be completed. Since a future may fail, this call is throwing.

```swift
/// Assume we get a future string back from some API
let futureString: Future<String> = ...

/// Block until the string is ready
let string = try futureString.wait()
print(string) /// String
```

!!! warning
    Do not use this method in route closures or controllers. Read the section about [Blocking](#blocking) for more information.

    
## Promise

Most of the time, you will be transforming futures returned by calls to Vapor's APIs. However, at some point you may need to create a promise of your own.

To create a promise, you will need access to an `EventLoop`. All containers in Vapor have an `eventLoop` property that you can use. Most commonly, this will be the current `Request`.

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

!!! info
    A promise can only be completed once. Any subsequent completions will be ignored.
    
### Thread Safety

Promises can be completed (`succeed(result:)` / `fail(error:)`) from any thread. This is why promises require an event-loop to be initialized. Promises ensure that the completion action gets returned to its event-loop for execution.

## Event Loop

When your application boots, it will usually create one event loop for each core in the CPU it is running on. Each event loop has exactly one thread. If you are familiar with event loops from Node.js, the ones in Vapor are very similar. The only difference is that Vapor can run multiple event loops in one process since Swift supports multi-threading.

Each time a client connects to your server, it will be assigned to one of the event loops. From that point on, all communication between the server and that client will happen on that same event loop (and by association, that event loop's thread). 

The event loop is responsible for keeping track of each connected client's state. If there is a request from the client waiting to be read, the event loop trigger a read notification, causing the data to be read. Once the entire request is read, any futures waiting for that request's data will be completed. 

### Worker

Things that have access to an event loop are called `Workers`. Every container in Vapor is a worker. 

The most common containers you will interact with in Vapor are:

- `Application`
- `Request`
- `Response`

You can use the `.eventLoop` property on these containers to gain access to the event loop.

```swift
print(app.eventLoop) // EventLoop
```

There are many methods in Vapor that require the current worker to be passed along. It will usually be labeled like `on: Worker`. If you are in a route closure or a controller, pass the current `Request` or `Response`. If you need a worker while booting your app, use the `Application`.

### Blocking

An absolutely critical rule is the following:

!!! danger
    Never make blocking calls directly on an event loop.
    
An example of a blocking call would be something like `libc.sleep(_:)`.

```swift
router.get("hello") { req in
    /// Puts the event loop's thread to sleep.
    sleep(5)
    
    /// Returns a simple string once the thread re-awakens.
    return "Hello, world!"
}
```

`sleep(_:)` is a command that blocks the current thread for the number of seconds supplied. If you do blocking work directly on an event loop, the event loop will be unable to respond to any other clients assigned to it for the duration of the blocking work. In other words, if you do `sleep(5)` on an event loop, all of the other clients connected to that event loop (possibly hundreds or thousands) will be delayed for at least 5 seconds.

Make sure to run any blocking work in the background. Use promises to notify the event loop when this work is done in a non-blocking way.

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

Not all blocking calls will be as obvious as `sleep(_:)`. If you are suspicious that a call you are using may be blocking, research the method itself or ask someone. Chances are if the function is doing disk or network IO and uses a synchronous API (no callbacks or futures) it is blocking.

!!! info
    If doing blocking work is a central part of your application, you should consider using a `BlockingIOThreadPool` to control the number of threads you create to do blocking work. This will help you avoid starving your event loops from CPU time while blocking work is being done.

    
