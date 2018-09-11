# Managing your project

Vapor 使用 Swift Package Manager (简称 SPM) 构建工程的源码和依赖. SPM 和 Cocoapods, Ruby gems 和 NPM 类似. 在大部分应用场景下, [Vapor Toolbox](toolbox.md) 代替你来与 SPM 沟通, 了解底层运作仍然很重要.

> tip
>
> 了解更多关于 SPM <a href="https://swift.org/package-manager/" target="_blank">Swift.org &rarr;</a> 

## 包清单

SPM 会首先检查你文件中的包清单: 位于工程根目录下的 `Package.swift`.

### Dependencies

Dependencies 中列出的是你的程序包依赖的其他 SPM 程序包, 所有 Vapor 应用程序都依赖 Vapor 软件包, 但你可以根据需求添加其他依赖项.

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

 上面的示例代码中, 3.0 或更高版本的 <a href="https://github.com/vapor/vapor" target="_blank">vapor/vapor &rarr;</a> 是这个程序包的依赖项, 向程序包中添加依赖项时, 需要标注那些 [targets](#targets) 依赖哪些最新可用的模块.

> warning
>
> 每次修改包清单的时候, 调用 `vapor update` 来实现更改.

### Targets

Target 是你程序包中所有的模块, 可执行文件和单元测试. 

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

大部分 Vapor app 包含三个 target, 尽管你也可以添加任意多的 target 来组织代码. 每个 target 声明他依赖的模块, 你必须在这里添加模块名字以将它 `引入` 到你的代码中. 一个 target 可以依赖项目中其他 target, 也可以依赖已经添加到 [main dependencies](#dependencies) 列表的包所暴露的 任何 target.

> tip 
>
> 可执行 target(包含 `main.swift` 文件的 target) 不可以被其他模块 `引入`, 所以 Vapor 有两个 target `App` 和 `Run`. 任何 `App` 中的代码都可以在 `AppTests` 中测试.

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

每个 `.target` 对应 `Sources` 文件夹中的文件夹, 
每个 `.testTarget` 对应 `Tests` 文件夹中的文件夹.

## 问题解决

如果你使用 SPM 时遇到问题, 清理项目有时会有帮助.

```sh
vapor clean
```
