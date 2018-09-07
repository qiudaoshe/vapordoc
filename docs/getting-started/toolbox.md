# 安装 Toolbox

Vapor 的命令行界面为常见任务提供了快捷工具

<img width="682" alt="Vapor Toolbox" src="https://cloud.githubusercontent.com/assets/1342803/23553208/26af9a0e-0020-11e7-8ed5-1ce09407ae8e.png">

Help 指令列出可用指令的说明和提示信息.

```sh
vapor --help
```

你也可以在其他指令中运行 `--help` 来获得提示.

```sh
vapor new --help
```

`--help` 指令应该作为你学习 Vapor 工具箱的第一选择, 因为它是紧跟更新版本的.

## New

创建新项目

```sh
vapor new <name>
```

`new` 指令的参数对应你的工程名称

!!! note
    工程名称应该遵循 <a href="http://wiki.c2.com/?PascalCase" target="_blank">Pascal 命名法 &rarr;</a>, 比如 `HelloWorld` 或 `MyProject`.

### 模板

Vapor 默认使用 API 模板创建新项目, 你也可以通过 `--template` 来选择不同的模板.

| Name | Flag                       | 描述                               |
|------|----------------------------|-----------------------------------|
| API  | `--template=api`           | JSON API with Fluent database.    |
| Web  | `--template=web`           | HTML website with Leaf templates. |
| Auth | `--template=auth-template` | JSON API with Fluent DB and Auth. |

> info
>
> GitHub 上有很多非官方的 Vapor 模板 <a href="https://github.com/search?utf8=✓&q=topic%3Avapor+topic%3Atemplate&type=Repositories" target="_blank">`vapor` + `template` topcs &rarr;</a>.
>
> 你可以将 GitHub URL 设置给 `--template` 来选择它们.

## Build & Run

编译和运行命令.

```sh
vapor build
vapor run
```

> tip
>
> 如果你使用 Mac 我们建议使用 [Xcode](xcode.md) 来编译和运行 Vapor app. 
>
> 因为这样速度更快, 而且你可以查看崩溃点! 
>
> 执行 `vapor xcode` 来生成 Xcode 项目.

## Updating

Vapor Toolbox 可以通过以下方法更新.

### Homebrew

```sh
brew upgrade vapor
```

### APT

```
sudo apt-get update
sudo apt-get install vapor
```