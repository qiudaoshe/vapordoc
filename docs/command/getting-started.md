# Getting Started with Command

Command (命令) 模块作为 Vapor 控制台模块 ([vapor/console](https://github.com/vapor/console)) 的一部分提供的. 此模块提供创建命令行界面 (CLI) 的 API. 它支撑着 Vapor Toolbox](../getting-started/toolbox.md).

>tip
>
>要深入了解命令模块所有 API, 查看 [Command API docs](https://api.vapor.codes/console/latest/Command/index.html).

## Usage

此包随 Vapor 一起提供并默认导出. 导入 `Vapor` 时你即拥有权限访问所有 `命令` API.

```swift
import Vapor // implies import Command
```

### Standalone

命令模块是庞大的 Vapor 包的一部分, 也可以独立运行于 Swift 项目.

要将它引入你的软件包, 将一下内容添加到你的 `Package.swift` 文件.

```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "Project",
    dependencies: [
        ...
        /// 💻 APIs for creating interactive CLI tools.
        .package(url: "https://github.com/vapor/console.git", from: "3.0.0"),
    ],
    targets: [
      .target(name: "Project", dependencies: ["Command", ... ])
    ]
)
```

使用 `import Command` 访问 API.

## Overview

继续访问 [Command → Overview](overview.md) 获得控制台预览.