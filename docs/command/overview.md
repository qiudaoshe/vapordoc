# Command Overview

本指引将通过如何创建 CLI 来向你介绍 Command (命令) 模块. 在示例中, 我们将实现 [`cowsay`](https://en.wikipedia.org/wiki/Cowsay), 这是一个打印奶牛 ASCII 图片的命令.

>tip
>
>你可以通过 `brew install cowsay` 安装真实的 `cowsay ` 程序.

```sh
$ cowsay Hello
  -----
< Hello >
  -----
          \   ^__^
           \  (oo\_______
              (__)\       )\/\
                   ||----w |
                   ||     ||
```

## Command

第一步创建一个遵守 [`Command`](https://api.vapor.codes/console/latest/Command/Protocols/Command.html) 协议的类型.

```swift
/// Generates ASCII picture of a cow with a message.
struct CowsayCommand: Command {
    ...
}
```

现在让我们所需函数.

### Arguments

命令可以有 0 或多个 [`CommandArgument (命令参数)`](https://api.vapor.codes/console/latest/Command/Structs/CommandArgument.html). 命令运行需要这些参数.

```swift
/// Generates ASCII picture of a cow with a message.
struct CowsayCommand: Command {
    /// See `Command`
    var arguments: [CommandArgument] {
        return [.argument(name: "message")]
    }
    
    ...
}
```

在这里, 我们定义了一个参数, 奶牛会说的"消息". 这是运行 `cowsay` 命令所需的.

### Options

命令可以有 0 或多个 [`CommandOption (命令选项)`](https://api.vapor.codes/console/latest/Command/Structs/CommandOption.html). 命令运行不需要这些选项, 选项可以使用 `--` 或 `-` 语法传递.

```swift
/// Generates ASCII picture of a cow with a message.
struct CowsayCommand: Command {
    ...
    /// See `Command`
    var options: [CommandOption] {
        return [
            .value(name: "eyes", short: "e", default: "oo", help: ["Change cow's eyes"]),
            .value(name: "tongue", short: "t", default: " ", help: ["Change cow's tongue"]),
        ]
    }
    ...
}
```

在这里, 我们定义了两个选项, `eye` 和 `tongue`. 它们可以让用户选择改变奶牛的外观.

### Help

接下来, 我们可以定义一个可选的帮助信息, 以便在用户输入 `--help` 时展示.

```swift
/// Generates ASCII picture of a cow with a message.
struct CowsayCommand: Command {
    ...
    /// See `Command`
    var help: [String] {
        return ["Generates ASCII picture of a cow with a message."]
    }
    ...
}
```

让我们看看命令完成后它的表现:

```sh
Usage: <executable> cowsay <message> [--eyes,-e] [--tongue,-t] 

Generates ASCII picture of a cow with a message.

Arguments:
  message n/a

Options:
     eyes Change cow's eyes
   tongue Change cow's tongue
```

### Run

最后, 我们要编写我们的实现:

```swift
/// Generates ASCII picture of a cow with a message.
struct CowsayCommand: Command {
    ...
    
    /// See `Command`.
    func run(using context: CommandContext) throws -> Future {
        let message = try context.argument("message")
        /// We can use requireOption here since both options have default values
        let eyes = try context.requireOption("eyes")
        let tongue = try context.requireOption("tongue")
        let padding = String(repeating: "-", count: message.count)
        let text: String = """
          \(padding)
        < \(message) >
          \(padding)
                  \\   ^__^
                   \\  (\(eyes)\\_______
                      (__)\\       )\\/\\
                        \(tongue)  ||----w |
                           ||     ||
        """
        context.console.print(text)
        return .done(on: context.container)
    }
}
```

[`CommandContext (命令内容)`](https://api.vapor.codes/console/latest/Command/Structs/CommandContext.html) 让你可以访问所需的一切, 包括一个 `容器`. 现在我们有了一个完整的 `命令`, 下一步就是配置它.

## Config

使用  [`CommandConfig (命令配置)`](https://api.vapor.codes/console/latest/Command/Structs/CommandConfig.html) 结构体向你的容器注册命令. 这通常在 [`configure.swift`](../getting-started/structure.md#configureswift) 中完成

```swift
/// Create a `CommandConfig` with default commands.
var commandConfig = CommandConfig.default()
/// Add the `CowsayCommand`.
commandConfig.use(CowsayCommand(), as: "cowsay")
/// Register this `CommandConfig` to services.
services.register(commandConfig)
```

使用 `--help` 查看你的指令是否正确配置.

```swift
swift run Run cowsay --help
```

成了!

```swift
$ swift run Run cowsay 'Good job!' -e ^^ -t U
  ---------
< Good job! >
  ---------
          \   ^__^
           \  (^^\_______
              (__)\       )\/\
                U  ||----w |
                   ||     ||
```