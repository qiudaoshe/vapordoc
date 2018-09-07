# Hello, world

到了这里, 你已经安装好了 Vapor, 让我们着手创建你的第一个 Vapor App 吧! 跟随指引, 一步步的创建, 编译, 运行你的 app.

## 新建工程

第一步, 在你的电脑上创建 Vapor 工程, 我们称它为 `Hello`.

打开终端, 执行 [Vapor 工具箱 `new`](toolbox.md#New) 指令.

```sh
vapor new Hello --branch=beta
```

指令执行完成后, 切换到新建的目录中.

```sh
cd Hello
```

## 生成 Xcode 工程

运行 [Vapor 工具箱 `xcode`](toolbox#xcode) 指令来生成 Xcode 工程

这样, 我们就可以像开发 iOS app 一样, 通过 Xcode 编译和运行.

```sh
vapor xcode
```

Vapor 工具箱会询问你, 是否设置为自动打开 Xcode, 选择 `yes`.

## 编译 和 运行

你现在应该打开了 Xcode, 从 scheme 列表中选择 [Run](xcode.md#run), 然后点击 play 运行,

你会在 Xcode 底部的终端窗口中看见:

```sh
Server starting on http://localhost:8080
```

## 访问 localhost

打开浏览器, 访问 <a href="http://localhost:8080/hello" target="_blank">localhost:8080/hello &rarr;</a>

你会看见如下页面:

```html
Hello, world!
```

恭喜, 你成功地创建, 编译, 运行了你的第一个 Vapor app! 🎉
