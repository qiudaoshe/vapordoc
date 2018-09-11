# Hello, world

到这里你已经安装好了 Vapor, 让我们创建你的第一个 Vapor App! 本指南会带你逐步创建新项目, 编译和运行.

## 新建工程

第一步是在你的电脑上新建 Vapor 项目, 在本指南中我们称它为 `Hello`.

打开终端, 执行 [Vapor Toolbox `新建`](toolbox.md#new) 指令.

```sh
vapor new Hello
```

指令执行完成, 切换到新建的目录中.

```sh
cd Hello
```

## 生成 Xcode 工程

运行 [Vapor Toolbox `xcode`](xcode.md) 指令来生成 Xcode 工程. 这让我们可以像开发 iOS app 一样, 通过 Xcode 编译和运行.

```sh
vapor xcode
```

toolbox 会询问你, 是否自动打开 Xcode, 选择 `yes`.

## 编译 和 运行

你现在应该打开了 Xcode, 从 scheme 列表中选择 [Run](xcode.md#run), 然后点击 play 运行,

你会在 Xcode 底部的终端窗口中看见:

```sh
Server starting on http://localhost:8080
```

## 访问本地主机

打开浏览器, 访问 <a href="http://localhost:8080/hello" target="_blank">localhost:8080/hello &rarr;</a>

你会看见如下页面:

```html
Hello, world!
```

恭喜, 你成功地创建, 编译和运行了你的第一个 Vapor app! 🎉
