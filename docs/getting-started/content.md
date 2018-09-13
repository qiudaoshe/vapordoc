# Content

在 Vapor 3 中, 所有的内容类型 (JSON, protobuf, URLEncodedForm, [Multipart](../multipart/getting-started.md) 等) 都被视为相同, 解析和序列化所需的只是一个 `Codable` 类型或者结构体.

对于本指南, 我们将使用 JSON 举例. 但请记住, 对于任何支持的内容类型, 使用的 API 都是相同的.

## Request

让我们看看如何解析下面的 HTTP 请求.

```http
POST /login HTTP/1.1
Content-Type: application/json

{
    "email": "user@vapor.codes",
    "password": "don't look!"
}
```

首先, 创建一个表示你期望的数据的结构体或者类.

```swift
import Vapor

struct LoginRequest: Content {
    var email: String
    var password: String
}
```

然后简单地让这个结构体或者类遵守 `Content` 协议.
现在我们做好准备解析这个 HTTP 请求了.

```swift
router.post("login") { req -> Future<HTTPStatus> in
    return req.content.decode(LoginRequest.self).map(to: HTTPStatus.self) { loginRequest in
        print(loginRequest.email) // user@vapor.codes
        print(loginRequest.password) // don't look!
        return .ok
    }
}
```

在这里使用 `.map(to:)` 因为 `req.content.decode(_:)` 返回一个 [future(期货)](async.md).

## Response

让我们看看该怎么创建以下 HTTP 响应.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "Vapor User",
    "email": "user@vapor.codes"
}
```

就像解析一样, 先创建一个表示你期望数据的结构体或者类.

```swift
import Vapor

struct User: Content {
    var name: String
    var email: String
}
```

然后让这个结构体或者类遵守 `Content` 协议.
现在我们做好准备序列化这个 HTTP 响应了.

```swift
router.get("user") { req -> User in
    return User(
        name: "Vapor User",
        email: "user@vapor.codes"
    )
}
```

现在你知道在 Vapor 中如何序列化和解析数据了!

>tip
>
>查看 [Vapor &rarr; Content](../vapor/content.md) 获得更多信息.

