---
title: "HttpRunner 4 快速上手"
date: 2022-07-27T10:50:29+08:00
categories: ["接口测试","未完成"]
tags: ["2022",'httprunner',"未完成"]
draft: false
---

> 这里是<a href="https://httprunner.com/docs/quickstart/" target="_blank">官方文档</a>

## 安装

**Linux or 类 unix 系统**一键安装：
```shell
$ sudo bash -c "$(curl -ksSL https://httprunner.com/script/install.sh)"
```

**Windows 系统**不支持一键安装，建议使用 `git bash` 运行上述命令下载包，然后手动添加`hrp.exe`到环境变量中。

如果没有 git 环境，则需要在项目 [Github Releases](https://github.com/httprunner/httprunner/releases) 页下载编译好的包，然后添加环境变量即可。

安装完成后在命令行执行 `hrp` ，没有报错就是成功安装了。

## 使用脚手架创建项目

HttpRunner 4 支持 go 和 Python 双执行引擎，同时支持自定义函数方法。目前支持使用 Python 和 Go编写自定义函数，
官方称自定义函数为插件。[官方描述点这里](https://httprunner.com/docs/user-guide/enhance-tests/debugtalk/)

### 使用默认方式(python插件)创建项目

**前提** ：需要 Python3.7+ 环境，且需 pip 安装同版本的 httprunner 包

执行 `hrp startproject projectName` 命令，即可初始化指定名称的项目工程。

生成的脚手架项目结构如下
```
文件夹 PATH 列表
卷序列号为 67F1-4164
D:.
│  .env
│  .gitignore
│  debugtalk.py
│  proj.json
│
├─har
│      .keep
│
├─reports
│      .keep
│
└─testcases
        demo.json
        ref_testcase.yml
        requests.json
        requests.yml
```

### 使用 go 插件创建项目

执行 `hrp startproject projectName --go`

生成的脚手架项目结构如下
```
文件夹 PATH 列表
卷序列号为 67F1-4164
D:.
│  .env
│  .gitignore
│  proj.json
│
├─har
│      .keep
│
├─plugin
│      debugtalk.go
│
├─reports
│      .keep
│
└─testcases
        demo.json
        ref_testcase.yml
        requests.json
        requests.yml
```

### 不使用插件创建项目

执行 `hrp startproject projectName --ignore-plugin`

生成的脚手架项目结构如下
```
文件夹 PATH 列表
卷序列号为 67F1-4164
D:.
│  .env
│  .gitignore
│  proj.json
│
├─har
│      .keep
│
├─reports
│      .keep
│
└─testcases
        requests.json
```

通过对比上述三种创建模式，不难看出主要区别就在于 debugtalk 文件的是否存在与文件格式。

如果你是Python语言使用者，或者是从 HttpRunner3 及以下版本迁移过来的，建议使用 Python 模式。

如果你是Go语言爱好者，建议使用 Go 模式。

不使用插件的方式适合于没有编程语言基础的用户。

## 运行接口测试

### Python 插件
测试用例就绪后，通过 hrp run 命令即可执行指定的测试用例；如需生成 HTML 测试报告，可附带 --gen-html-report 参数。

```hrp run testcases/ref_testcase.yml --gen-html-report```

### go 插件
- 编写用例
- 编译 debugtalk.go 文件

    ```hrp build .\plugin\debugtalk.go -o dest_path  // dest_path 为项目根路径 ```
- 运行用例
    ```hrp run testcases/ref_testcase.yml --gen-html-report```