# Structure

本章对一个典型的 Vapor 项目的目录结构进行分析, 从而帮助你了解 Vapor 的项目组成.

## 目录结构

Vapor 的文件目录结构是建立在 [SPM 目录结构](spm#folder-structure) 之上的.

```
.
├── Public
├── Sources
│   ├── App
│   │   ├── Controllers
│   │   ├── Models
│   │   ├── boot.swift
│   │   ├── configure.swift
│   │   └── routes.swift
│   └── Run
│       └── main.swift
├── Tests
│   └── AppTests
└── Package.swift
```

让我们了解一下每个目录和文件的作用.

## Public

Public 目录包含由应用程序提供的公共文件, 通常包括图片, 网页样式列表和浏览器脚本等.

当 Vapor 响应请求时, 它首先检查请求项是否在此文件夹中, 如果是, 则跳过应用程序逻辑并立即返回文件.

比如说, 对 `localhost:8080/favicon.ico` 的请求会检查 `Public/favicon.ico` 是否存, 如果存在 Vapor 将返回它.

## Sources

Sources 目录包含项目的所有 Swift 源文件, 顶级目录 (`App` 和 `Run`) 反应程序包模块, 就像声明在 [包清单](spm#targets) 中的那样.

### App

App 目录是应用程序最重要的文件夹, 它是所有的应用程序逻辑的所在.

#### Controllers

controller 是将应用程序逻辑组合在一起的好方法, 大多数 controller 都有很多接收请求, 并且返回某种响应的函数.

> tip
>
> Vapor 支持, 单并不强制使用 MVC 设计模式

#### Models

Models 目录是存储 [`Content`](content.md) 结构体, 或者 Fluent [`Model`](../fluent/models.md) 模型的好地方.

#### boot.swift

这个文件包括一个在程序启动 _之后_, 运行 _之前_ 被调用的函数, 这是一个很好的地方做每次应用程序启动时应该发生的事情.

在这里你可以访问 [`Application`](application.md) 并用它来创建 [服务](application.md#services).

#### configure.swift

这个文件包含一个函数, 该函数接收应用程序的配置, 环境和服务作为输入. 这是更改配置或向应用程序注册 [服务](application.md#services) 的好地方.

#### routes.swift

这个文件包含用于向路由器添加路由的功能.

你会注意到模板已经帮你添加了一个示例路由返回 "Hello, world!" 响应.

你可以根据需要创建任意数量的函数来进一步组织代码, 只需确保在这个主要路由集合中调用他们.

## Tests

每一个 `Sources` 文件夹中的中不可执行的模块都有相应的 `...Tests` 目录.

### AppTests

这个目录包含 `App` 模块代码的单元测试. 点击 [测试 &rarr; 入门](../testing/getting-started.md) 了解更多.

## Package.swift

SPM 的 [包清单](spm.md#package-manifest).