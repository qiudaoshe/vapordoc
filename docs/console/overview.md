# Console Overview

本指引将简单介绍 Console (控制台) 模块, 向你展示如何格式化输出文本和请求用户输入.

## Terminal

名为 [`Terminal (终端)`](https://api.vapor.codes/console/latest/Console/Classes/Terminal.html) 的 [`Console (控制台)`](https://api.vapor.codes/console/latest/Console/Protocols/Console.html) 协议的默认实现供你使用.

```swift
let terminal = Terminal()
print(terminal is Console) // true
terminal.print("Hello")
```

本指引的剩余部分将假设一个通用的 `控制台`. 但直接使用 `终端` 也可以实现. 你可以使用任何可用的 [`容器`](https://api.vapor.codes/service/latest/Service/Protocols/Container.html) 来创建一个控制台.

```swift
let console = try req.make(Console.self)
console.print("Hello")
```

## Output

[`控制台`](https://api.vapor.codes/console/latest/Console/Protocols/Console.html) 提供了集中输出字符串的便捷方法, 比如 `print(_:)` 和 `warning(_:)`. 所有这些函数最终都调用 `output(_:)`, 最强大的输出函数. 这个函数接受 [`控制台`](https://api.vapor.codes/console/latest/Console/Structs/ConsoleText.html), 它支持独立样式的字符串组件.

```swift
/// Prints "Hello, world", but the word 'world' is blue.
console.output("Hello, " + "world".consoleText(color: .blue))
```

你可以根据需要组合多个不同样式的片段到 [`ConsoleText (控制台文本)`](https://api.vapor.codes/console/latest/Console/Structs/ConsoleText.html) 中. 所有输出文本的 [`控制台`](https://api.vapor.codes/console/latest/Console/Protocols/Console.html) 函数都应该有一个重载来接受 [`控制台文本`](https://api.vapor.codes/console/latest/Console/Structs/ConsoleText.html).

## Input

[`控制台`](https://api.vapor.codes/console/latest/Console/Protocols/Console.html) 提供几种请求用户输入的函数, 最基本的是 `input(isSecure:)`.


```swift
/// Accepts input from the terminal until the first newline.
let input = console.input()
console.print("You wrote: \(input)")
```

### Ask

使用 `ask(_:)` 为用户提供提示和输入指示器.

```swift
/// Outputs the prompt then requests input.
let name = console.ask("What is your name?")
console.print("You said: \(name)")
```

上述代码会输出:

```sh
What is your name?
> Vapor
You said: Vapor
```

### Confirm

使用 `confirm(_:)` 为用户提供 是/否 输入.

```swift
/// Prompts the user for yes / no input.
if console.confirm("Are you sure?") {
    // they are sure
} else {
    // don't do it!
}
```

上述代码会输出:

```swift
Are you sure?
y/n> yes
```

>note
>
>`confirm(_:)` 将继续提示用户, 直到用户回复 yes 或 no.
    