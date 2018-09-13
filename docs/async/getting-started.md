# Getting Started with Async

Async 模块是作为 Vapor Core ([vapor/core](https://github.com/vapor/core)) 的一部分提供给的, 它是在 [Swift NIO](https://github.com/apple/swift-nio) 之上构建的便利 API (主要是扩展) 的集合

>tip
>
>你可以在 Async 的 GitHub [README](https://github.com/apple/swift-nio/blob/master/README.md) 或者 [API Docs](https://apple.github.io/swift-nio/docs/current/NIO/index.html) 上阅读到更多关于 SwiftNIO 的异步类型 (`Future`,  `Promise`, `EventLoop`等).

## Usage

这个功能包随 Vapor 一同引入并默认被导出供使用. 在你导入 `Vapor` 时你可以访问所有的 `Async` API.

```swift
import Vapor // implies `import Async`
```

### Standalone

Async 模块作为庞大的 Vapor Core 软件包的一部分, 也可以单独与任何 Swift 项目一期使用.

将以下内容添加到 `Package.swift` 文件, 来将其包含到你的软件包.

```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "Project",
    dependencies: [
        ...
        .package(url: "https://github.com/vapor/core.git", from: "3.0.0"),
    ],
    targets: [
      .target(name: "Project", dependencies: ["Async", ... ])
    ]
)
```

通过 `import Async` 来访问 API.

## Overview

继续访问 [Async &rarr; Overview](overview.md), 了解 Async 的功能概述.

