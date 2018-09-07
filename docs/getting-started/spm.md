# Managing your project

Vapor 使用 Swift Package Manager (简称 SPM) 构建工程的源码和依赖. SPM 和 Cocoapods, Ruby gems 和 NPM 类似. 在大部分应用场景下, [Vapor Toolbox](toolbox.md) 代替你来与 SPM 沟通, 了解底层运作仍然很重要.

> tip
>
> 了解更多关于 SPM <a href="https://swift.org/package-manager/" target="_blank">Swift.org &rarr;</a> 

## 包清单

SPM 会首先检查你文件中的包清单: 位于工程根目录下的 `Package.swift`.

### Dependencies

Dependencies 中列出的是你工程中 SPM 管理的包, 所有 Vapor 工程都依赖 Vapor 包, 你可以在这里添加你需要的依赖.

```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "VaporApp",
    dependencies: [
        // 💧 A server-side Swift web framework. 
        .package(url: "https://github.com/vapor/vapor.git", from: "3.0.0-rc"),
    ],
    targets: [ ... ]
)
```

 上面的示例代码中, 3.0 或更高版本的 <a href="https://github.com/vapor/vapor" target="_blank">vapor/vapor &rarr;</a> 是这个软件包的依赖, 当你添加依赖的时候, 你需要确保依赖是最近可用的, 并且标注你的 [targets](#targets)

> warning
>
> 每次修改包清单的时候, 执行 `vapor update` 来更新.

### Targets

Targets 是你软件包中所有的模块, 可执行文件和单元测试. 

```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "VaporApp",
    dependencies: [ ... ],
    targets: [
        .target(name: "App", dependencies: ["Vapor"]),
        .target(name: "Run", dependencies: ["App"]),
        .testTarget(name: "AppTests", dependencies: ["App"]),
    ]
)
```

大部分 Vapor app 包含三个 target, 你也可以按需添加. 每个 target 声明他依赖的模块, 你需要在添加模块名字以将它 `引入` 到你的代码中. 一个 target 可以依赖你工程中或者已经添加到[dependencies](#dependencies) 列表中的target.

> tip 
>
> 可执行 target(包含 `main.swift` 文件的 target) 不可以被其他模块 `引入`, 所以 Vapor 有两个 target `App` 和 `Run`. 而从上述代码可以看出, `App` 由 `AppTests` 进行测试.

## 文件结构

下面是典型的 SPM 文件结构

```
.
├── Sources
│   ├── App
│   │   └── (Source code)
│   └── Run
│       └── main.swift
├── Tests
│   └── AppTests
└── Package.swift
```

每个 `.target` 对应 `Sources` 文件夹下的目录, 
每个 `.testTarget` 对应 `Tests` 文件夹下的目录.

## 问题解决

如果你使用 SPM 时遇到问题, 清理工程有时会有帮助.

```sh
vapor clean
```
