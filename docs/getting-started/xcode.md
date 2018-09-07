# Xcode

如果你使用 Mac 平台开发, 那么就可以使用 Xcode 开发, 通过 Xcode 可以编译, 运行和停止服务, 还可以使用 breakpoints 和 instruments 来调试代码.

<img width="1072" alt="Xcode 9 running Vapor" src="https://user-images.githubusercontent.com/1342803/32910761-1f8dd56e-cad8-11e7-9869-feccf89f775e.png">

使用 Xcode 开发 Vapor app 是很棒的选择, 但不是唯一, 你也可以使用其他编辑器.

## 生成 Xcode 工程

为了能够使用 Xcode 开发, 你只需要执行 [Vapor 工具箱](toolbox.md) 指令.

```sh
vapor xcode
```

> tip	
>
> 不用担心提交生成的 Xcode 项目到 git, 只需要在使用的时候生成新的 Xcode 即可.

## 运行

编译和运行 Vapor app之前, 首先在 scheme 列表中选择 `Run`, 然后在设备列表中选择 `My Mac`

<img width="434" alt="Run Scheme" src="https://user-images.githubusercontent.com/1342803/32917883-944f3f30-caee-11e7-980f-860ee70bd873.png">

正确选择之后, 点击运行按钮或者按键盘快捷键 `Command + R`.

## 测试

如果进行进行单元测试, 在 scheme 列表中选择 `<项目名>-Package`, 然后按键盘快捷键 `Command + U`

> warning
>
> 在 scheme 列表中有一些无关的 scheme, 无视就好!