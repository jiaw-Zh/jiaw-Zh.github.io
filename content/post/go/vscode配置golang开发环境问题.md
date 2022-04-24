---
title: "Vscode 配置 Golang 开发环境问题"
date: 2022-04-24T10:22:41+08:00
categories: [Golang]
tags: ["2022",'Golang']
toc: false
---

> 单目录下多项目使用

最近在学习 go 一样时，在单个目录下创建了多个小项目，目录结构大致如下

```
├─tonybai.go
    ├─array
    │      main.go
    ├─bookstore
    │  │  go.mod
    │  │  go.sum
    │  │  main.exe
    │  ├─cmd
    │  │  └─bookstore
    │  │          main.go
    │  ├─internal
    │  │  └─store
    │  │          memstore.go
    │  ├─server
    │  │  │  server.go
    │  │  │
    │  │  └─middleware
    │  │          midderware.go
    │  └─store
    │      │  store.go
    │      │
    │      └─factory
    │              factory.go
    ├─helloworld
    │      helloworld.go
    ├─map
    │      main.go
    ├─string
    │      main.go
    └─structlearn
        │  go.mod
        │  main.go
        └─book
                book.go
```

tonybai.go 目录下还存在多个文件夹，且每个文件夹都为一个独立的项目

当 structlearn 和 bookstore 目录下各有一个 go.mod 文件时，vscode 开始报错：

```
errors loading workspace: You are outside of a module and outside of $GOPATH/src.
If you are using modules, please open your editor to a directory in your module.
```

排查加检索问题之后，发现问题来自于 `gopls`

解决方案是在 vscode 的 settings.json 文件中，加入下面的内容：

```
"gopls": {
    "experimentalWorkspaceModule": true
},
```

在官方文档找到的说明：
```
experimentalWorkspaceModule bool
This setting is experimental and may be deleted.

experimentalWorkspaceModule opts a user into the experimental support for multi-module workspaces.

Default: false.
```