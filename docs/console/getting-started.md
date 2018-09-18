# Getting Started with Console

Console (控制台) 模块是作为 Vapor 控制台包 ([vapor/console](https://github.com/vapor/console)) 的一部分提供的. 此模块提供用于执行控制台 I/O 的 API, 包括输出格式化文本, 请求用户输入, 显示诸如加载进度条等活动指示器.

>tip
>
>想要深入了解所有的控制台 API, 请查看 [控制台 API 文档](https://api.vapor.codes/console/latest/Console/index.html)

## 导入

此包随 Vapor 一起提供并默认导出. 导入 `Vapor` 时你即拥有权限访问所有 `控制台` API.

```swift
import Vapor // implies import Console
```

### 独立性

控制台模块是庞大的 Vapor 包的一部分, 也可以独立运行于 Swift 项目.

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
      .target(name: "Project", dependencies: ["Console", ... ])
    ]
)
```

使用 `import Console` 访问 API.

## Overview

继续访问 [Console → Overview](overview.md) 获得控制台预览.