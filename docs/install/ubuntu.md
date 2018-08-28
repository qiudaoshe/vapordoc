# Ubuntu 平台

在 Ubuntu 平台安装 Vapor 只需要几分钟即可.

## 支持版本

Vapor 与 Swift 支持同样版本的 Ubuntu.

| 版本     | 代号         |
|---------|--------------|
| 16.10   | Yakkety Yak  |
| 16.04   | Xenial Xerus |
| 14.04   | Trusty Tahr  |

## APT Repo

为 Vapor 添加 APT repo 来使用 Vapor 的 Ubuntu package.

### 快速安装脚本

运行以下脚本就可以轻松添加 APT repo.

```sh
eval "$(curl -sL https://apt.vapor.sh)"
```

!!! tip
	这个指令需要 `curl` 支持, 可以运行 `sudo apt-get install curl` 来安装.

### Dockerfile
如果使用 Dockerfile 配置 Ubuntu, 可以通过以下指令完成 APT repo 的添加:
```sh
RUN /bin/bash -c "$(wget -qO- https://apt.vapor.sh)"
```

### 手动添加

也可以以下指令手动完成添加.

```sh
wget -q https://repo.vapor.codes/apt/keyring.gpg -O- | sudo apt-key add -
echo "deb https://repo.vapor.codes/apt $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/vapor.list
sudo apt-get update
```

## 安装 Vapor

现在你已经添加好了 Vapor 的 APT repo, 执行以下指令,你可以开始安装需要的依赖了.

```sh
sudo apt-get install swift vapor
```

### 验证安装

执行下指令, 来再次验证安装成功.

#### Swift

```sh
swift --version
```

你会看到这样的输出信息:

```sh
Apple Swift version 4.1.0 (swiftlang-900.0.69.2 clang-900.0.38)
Target: x86_64-apple-macosx10.9
```

Vapor 支持 Swift 4.1 或更高版本.

#### Vapor 工具箱

```sh
vapor --help
```

你会看到可使用指令的列表.

## 完成

现在你完成了使用 Vapor 的必要安装, 前往 [进阶 &rarr; Hello, world](../getting-started/hello-world.md) 来创建你的app.

## Swift.org

如果想要获得关于 Swift 4.1 的更多使用指导, 查看 [Swift.org](https://swift.org) 的 [Using Downloads](https://swift.org/download/#using-downloads).
