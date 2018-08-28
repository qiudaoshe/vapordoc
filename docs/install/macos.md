# macOS 平台

想要在 macOS 平台使用 Vapor, 你需要安装 Xcode 9.3 或更高版本.

## 安装 Xcode

从 Mac App Store 获取并安装 [Xcode 9.3 或更高版本](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).

<img width="1112" alt="Xcode 9.1" src="https://user-images.githubusercontent.com/1342803/32911091-1b55b434-cad9-11e7-8ab2-fbd7ea0084da.png">

!!! 注意
    当 Xcode 下载完成, 你需要打开并完成安装. 这可能需要花费一点时间.

### 验证安装

打开终端并运行以下指令, 来再次验证 Xcode 安装成功:

```sh
swift --version
```

你会看到这样的输出信息:

```sh
Apple Swift version 4.1.0 (swiftlang-900.0.69.2 clang-900.0.38)
Target: x86_64-apple-macosx10.9
```

!!! beta
    如果你的 Xcode 是 beta 版本, 那么在" Xcode &rarr; Preference &rarr; Locations &rarr; Command Line Tools" 选项中选择你的 Xcode 版本.

Vapor 支持 Swift 4.1 或更高版本.

## 安装 Vapor

现在你安装好了 Swift 4.1, 让我们安装 [Vapor 工具箱](../getting-started/toolbox.md).

Vapor 工具箱包含所有 Vapor 的依赖, 其中也包含创建新工程的命令行工具.

```sh
brew install vapor/tap/vapor
```

!!! tip
    如果你没有安装 Homebrew, 前往 <a href="https://brew.sh" target="_blank">brew.sh &rarr;</a>

### 验证安装

打开终端并运行以下指令, 来再次验证 Vapor 安装成功:

```sh
vapor --help
```

你会看到可使用指令的列表.

## 完成

现在你完成了使用 Vapor 的必要安装, 前往 [进阶 &rarr; Hello, world](../getting-started/hello-world.md) 来创建你的app.
