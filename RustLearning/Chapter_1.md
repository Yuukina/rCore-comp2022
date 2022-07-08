# 1. 寻找牛刀，以便小试

## 1.1 安装Rust环境

Linux环境安装。

```shell
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

> Q：Command 'curl' not found.
>
> A：sudo snap install curl  # version 7.82.0, or
>       **<u>sudo apt install curl</u>**  # version 7.68.0-1ubuntu2.12
>
> [curl](https://curl.se/) 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。
>
> [snap](https://www.jianshu.com/p/70622b2e9c5d) 是Canonical公司发布的全新的软件包管理方式。

> Q：在这里采用snap安装后，仍然安装失败。
>
> A：Ubuntu 允许我们使用 apt 或 snap 安装两个不同版本的 curl。我首先选择 snap 来安装它，然后我收到了错误消息。当我删除 snap 版本并使用 apt 安装 curl 时，rust install 没有任何问题。最新的 curl 对此有一些不兼容。

![Rust安装成功](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220707223229388.png)

![查看相关版本](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220707223530355.png)

## 1.2 VSCode 插件

- `rust-analyzer`，社区驱动的Rust插件，推荐:happy:

- `Even Better TOML`，支持 .toml 文件完整特性
- `Error Lens`，更好的获得错误展示
- `CodeLLDB`，Debugger 程序

## 1.3 Cargo

### 创建项目 cargo new

![目录结构](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220707230143373.png)

```shell
cargo new world_hello [--bin]
cd world_hello
```

`cargo new` 创建项目。

Rust 项目主要分为两个类型：`bin` 和 `lib`，前者是一个可运行的项目，后者是一个依赖库项目。目前的版本，`cargo` 会**默认**创建 `bin` 类型的项目。

> Q：VSCode 中 files.exclude 可以设置隐藏哪些文件（夹）。

### 运行项目 cargo run

![编译成功](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220708114952315.png)

1. `cargo run`，在之前创建的 `world_hello` 目录下运行

   1. ![cargo run](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220708114045459.png)
   2. 实际上是先编译后运行。

2. 手动编译和运行项目

   1. `debug` 模式，编译快，运行慢，方便开发。

      ![debug](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220708114705520.png)

   2. `release` 模式，获得高性能代码

      ![release](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220708114901496.png)

### 检查编译 cargo check

`cargo check` 是我们在代码开发过程中最常用的命令，它的作用很简单：快速的检查一下代码能否编译通过。因此该命令速度会非常快，能节省大量的编译时间。

### Cargo.toml 和 Cargo.lock

![image-20220708121419830](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220708121419830.png)

- `Cargo.toml` 是 `cargo` 特有的**项目数据描述文件**。
- `Cargo.lock` 文件是 `cargo` 工具根据同一项目的 `toml` 文件生成的**项目依赖详细清单**。

#### package

`package` 中记录了项目的描述信息。

`name` 字段定义了项目名称，`version` 字段定义当前版本，新项目默认是 `0.1.0`，`edition` 字段定义了我们使用的 Rust 大版本。

#### dependencies

`dependencies` 描述该项目的各种依赖项。

- 基于 Rust 官方仓库 `crates.io`，通过版本说明来描述
- 基于项目源代码的 git 仓库地址，通过 URL 来描述
- 基于本地项目的绝对路径或者相对路径，通过类 Unix 模式的路径来描述

```toml
[dependencies]
rand = "0.3"
hammer = { version = "0.5.0"}
color = { git = "https://github.com/bjz/color-rs" }
geometry = { path = "crates/geometry" }
```