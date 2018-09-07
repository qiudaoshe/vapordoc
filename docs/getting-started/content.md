# Content

Vapor 3 对所有的 content 类型 (JSON, protobuf, URLEncodedForm, [Multipart](../multipart/getting-started.md 等) 都一视同仁, 你只需要一个 `Codable` 类或者结构体.

为了说明, 我们将使用 JSON 举例. 同样, 使用的 API 对任何支持的 content 类型都适用.

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

首先, 创建一个结构体或者类来表示你想要的数据.

```swift
import Vapor

struct LoginRequest: Content {
    var email: String
    var password: String
}
```

然后让这个结构体或者类遵守 `Content` 协议.
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

在这里使用 `.map(to:)` 因为 `req.content.decode(_:)` 返回值是 [future](async.md).

## Response

让我们看看该怎么创建接下来的 HTTP 响应.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "Vapor User",
    "email": "user@vapor.codes"
}
```

类似解析步骤, 先创建一个结构体或者类来表示你想要的数据.

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

