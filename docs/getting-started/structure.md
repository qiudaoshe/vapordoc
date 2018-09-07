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

Public 目录包含 Vapor 将要响应外部请求并提供的公共文件, 包括图片, 网页样式列表和浏览器执行脚本等.

当 Vapor 响应请求时, 它自身会首先检查否在 Public 文件夹中存在请求项, 如果存在, Vapor 立即返回请求项, 而忽略 app 的程序逻辑.

比如说, 一条 `localhost:8080/favicon.ico` 请求会检查 `Public/favicon.ico` 是否存在于 Public 中, 如果存在就会立即返回.

## Sources

Sources 目录包含 Vapor 程序的 Swift 源文件, 第一层级目录 (`App` 和 `Run`) 对应声明在 [包清单](spm#targets) 中的 package.

### App

App 目录是 Vapor 程序最重要的, 所有的逻辑都将在这里实现.

#### Controllers

通过 controller 可以更好的组织程序, 大多数 controller 包含很多方法, 可以接收请求, 并且回应.

> tip
>
> Vapor 支持, 单并不强制使用 MVC 设计模式

#### Models

Models 目录用来存放 [`Content`](content.md) 结构体, 或者 Fluent [`Model`](../fluent/models.md).

#### boot.swift

这个文件的主方法在程序 boot _之后_, 运行 _之前_ 被调用, 一般在这里可以执行每次程序运行起来之前需要执行的代码.

在这里你可以访问 [`Application`](application.md) 并用它来创建 [服务](application.md#services).

#### configure.swift

这个文件包的主方法接收配置文件, 环境和程序接收的服务. 一般在这里更改服务器配置, 或者注册 [服务](application.md#services).

#### routes.swift

这个文件的主方法是用来给 router 添加路由的.

你会发现模板已经帮你添加了一个示例路由, 它返回 "Hello, world!".

你可以在这里创建方法来更好的组织代码, 只要确保在这里被调用.

## Tests

任何在 `Sources` 中不可执行的模块都应该对应一个 `...Tests` 目录.

### AppTests

这个目录包含 `App` 模块的单元测试代码. 点击 [测试 &rarr; 入门](../testing/getting-started.md) 了解更多.

## Package.swift

SPM 的 [包清单](spm.md#package-manifest).