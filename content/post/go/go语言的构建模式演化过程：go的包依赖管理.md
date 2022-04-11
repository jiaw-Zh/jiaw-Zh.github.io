---
title: "Go语言的构建模式演化过程——Go的包依赖管理"
date: 2022-04-11T14:51:29+08:00
categories: [Golang]
tags: ["2022",'Golang','Go Module'] 
toc: true
---

> 重新整理于 [Tony Bai · Go 语言第一课](https://time.geekbang.org/column/article/429941)

> Go Module 已经是 go 官方标准包依赖管理和构建模式了，所以从 go 入门开始就建议直接使用 Go Module。

Go 程序是由 Go 包组合而成的，Go 程序的构建过程就是**确定包版本、编译包以及将编译后得到的目标文件链接在一起的过程**。

Go 语言的构建模式历经了三个迭代和演化过程，分别是最初期的 GOPATH、1.5 版本的 Vendor 机制，以及现在的 Go Module。

## GOPATH

Go 语言在首次开源时，就内置了一种名为 GOPATH 的构建模式。在这种构建模式下，Go 编译器可以在**本地 GOPATH 环境变量配置的路径**下，搜寻 Go 程序依赖的第三方包。如果存在，就使用这个本地包进行编译；如果不存在，就会报编译错误。

GOAPTH 的路径可以通过命令 `go env GOPATH` 查看，默认值是 `$HOME/go`

使用 GOPATH 进行构建时，代码总是会保存在 `$GOPATH/src` 目录下，在工程经过 `go build`、`go install` 或 `go get` 等指令后，会将产生的二进制可执行文件放在 `$GOPATH/bin` 目录下，生成的中间缓存文件会被保存在 `$GOPATH/pkg` 下。

那么在 `GOPATH` 构建模式下，Go 编译器在编译 Go 程序时，就会在 `$GOPATH` 路径下搜索第三方依赖包是否存在，如果没有找到我们可以通过 `go get` 命令将本地缺失的第三方依赖包下载到本地，比如：

```shell
$ go get github.com/sirupsen/logrus
```

这里的 go get 命令，不仅能将 logrus 包下载到 GOPATH 环境变量配置的目录下，它还会检查 logrus 的依赖包在本地是否存在，如果不存在，go get 也会一并将它们下载到本地。

不过，在 GOPATH 构建模式下，Go 编译器实质上并**没有关注 Go 项目所依赖的第三方包的版本**。但 Go 开发者希望自己的 Go 项目所依赖的第三方包版本能受到自己的控制，而不是随意变化。于是 Go 核心开发团队引入了 Vendor 机制试图解决上面的问题。

## Vendor 机制

Go 在 1.5 版本中引入 vendor 机制。vendor 机制本质上就是在 Go 项目的某个特定目录下，将项目的所有依赖包缓存起来，这个特定目录名就是 vendor。

Go 编译器会优先感知和使用 vendor 目录下缓存的第三方包版本，而不是 GOPATH 环境变量所配置的路径下的第三方包版本。这样，无论第三方依赖包自己如何变化，无论 GOPATH 环境变量所配置的路径下的第三方包是否存在、版本是什么，都不会影响到 Go 程序的构建。

下面是一个使用 vendor 机制的项目结构：

```shell
.
├── main.go
└── vendor/
    ├── github.com/
    │   └── sirupsen/
    │       └── logrus/
    └── golang.org/
        └── x/
            └── sys/
                └── unix/
```

这种方式虽然一定程度解决了 Go 程序可重现构建的问题，但它也有如下两个弊端：
- 要想开启 vendor 机制，你的 Go 项目必须位于 GOPATH 环境变量配置的某个路径的 src 目录下面，且庞大的 vendor 目录需要同步到代码仓库
- 你还需要手工管理 vendor 下面的 Go 依赖包，包括项目依赖包的分析、版本的记录、依赖包获取和存放，等等

## Go Module

从 Go 1.11 版本开始，除了 GOPATH 构建模式外，Go 又增加了一种 Go Module 构建模式。

### 创建一个 Go Module

1. 通过 `go mod init module path` 创建 go.mod 文件，将当前项目变为一个 Go Module 
2. 通过 `go mod tidy` 命令自动更新当前项目的依赖信息
3. 通过 `go build` 执行新的 module 的构建

一个 go mod 生成实例：

```shell
$ go mod init github.com/bigwhite/module-mode
go: creating new go.mod: module github.com/bigwhite/module-mode
go: to add module requirements and sums: 
  go mod tidy
```

1. 使用 github.com/... 作为 module path 是因为多数实用级 module 多是上传到 github 上的。用这种示例便于后续与真实生产接驳。但对于本地开发使用的简单示例程序而言，module path 可以任意起，比如：module/demo1 也是ok的
2. 编译时可以按提示手动添加依赖包及版本，也可以使用 go mod tidy 命令自动添加依赖包的版本信息到 go.mod 文件中
3. go mod tidy 下载的第三方包一般在 $GOPATH/pkg/mod 下面

执行完 go mod tidy 后，当前项目除了 go.mod 文件外，还多了一个新文件 go.sum，这同样是由 go mod 相关命令维护的一个文件，它存放了特定版本 module 内容的哈希值。


### Go Module的4类常规操作

1. 为当前 module 添加一个依赖
    1. 更新源码
    2. 使用go get 或 go mod tidy
2. 升级 / 降级依赖的版本
    1. 使用带版本号的 go get：`go get github.com/sirupsen/logrus@v1.7.0`
    2. 使用万能的 go mod tidy
    ```shell
    $go mod edit -require=github.com/sirupsen/logrus@v1.7.0
    $go mod tidy
    ```
3. 移除一个依赖
    1. 修改源码
    2. 万能的 go mod tidy
4. 特殊情况：使用 vendor
    1. go mod vendor
    2. 基于 vendor 构建，需要在 go build 后面加上 -mod=vendor 参数