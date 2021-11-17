---
layout: post
title:  "使用 GitBook 发布你的书"
date:   2021-10-21 16:25:00 +0800
categories: ['工具']
tags:  ['2021' , 'Gitbook']
toc: true
---

> GitBook 现在已经发展成了一个商业公司，但是它的旧版本依然在互联网上为我们提供免费服务。

## 介绍

GitBook 是一个使用 Git 和 Markdown 来构建书籍的工具。它可以将你的书输出很多格式：PDF，ePub，mobi，或者输出为静态网页。

GitBook 工具链是开源并且完全免费的，它的源码可以在 [GitHub](https://github.com/GitbookIO/gitbook) 上获取。

[GitBook.com](https://www.gitbook.com/) 现已发展为一个商业公司，使用他们的产品需要付费。

如果你想看更详细的教程可以看 [这里](http://www.chengweiyang.cn/gitbook/index.html)，本文只提供可以使你完成工作的必要内容。

## 安装

首先你需要安装 [nodejs](http://nodejs.cn/download/current/) 

**此处建议安装 12.0 以下版本的 nodejs，可以省去好多麻烦**

然后在命令行执行下面这条命令就 OK 啦！

```shell
$ npm install gitbook-cli -g
```

使用这条命令检测是否成功安装：

```shell
$ gitbook -V
CLI version: 2.3.2
Installing GitBook 3.2.3
C:\Users\YQ-Noel\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287
      if (cb) cb.apply(this, arguments)
                 ^

TypeError: cb.apply is not a function
    at C:\Users\YQ-Noel\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\polyfills.js:287:18
    at FSReqCallback.oncomplete (fs.js:193:5)
```

上面出现报错的原因是 node 版本问题，解决方案是：

打开 `C:\Users\${你的用户名}\AppData\Roaming\npm\node_modules\gitbook-cli\node_modules\npm\node_modules\graceful-fs\**polyfills.js**` 文件，注释 62 到 64 这三行代码：

```
//fs.stat = statFix(fs.stat)
//fs.fstat = statFix(fs.fstat)
//fs.lstat = statFix(fs.lstat)
```

再次运行

```shell
$ gitbook -V
...
# 省略中间一大段安装 gitbook 的日志
CLI version: 2.3.2
GitBook version: 3.2.3
```

## 主要命令

### 初始化书籍

```shell
$ gitbook init ${book_name}
```

初始化之后会在目录下生成一个 `README.md` 文件，同时你还需要手动创建一个目录文件 `SUMMARY.md`。

`README.md` 是对书籍的简单介绍。

`SUMMARY.md` 是书籍的目录结构。内容如下：

```shell
$ cat book/SUMMARY.md 
# SUMMARY

- [Chapter1](chapter1/README.md)
  - [Section1.1](chapter1/section1.1.md)
  - [Section1.2](chapter1/section1.2.md)
- [Chapter2](chapter2/README.md)
```

### 编译和预览

完成上述操作之后你就可以直接编译并预览书籍了，使用如下命令：

```shell
$ gitbook serve
```

运行之后就可以在浏览器打开 `http://localhost:4000` 预览书籍了。

`gitbook serve` 命令执行了两个步骤，编译并预览，你也可以只编译书籍

```shell
$ gitbook build
```

执行完之后一样会在目录下生成一个 `_book` 文件夹，分享给其他人部署的时候，可以只发这个文件夹。

### 编辑

编辑书籍推荐使用 [VS Code](https://code.visualstudio.com/)

### 导出书籍

`gitbook` 可以很方便的导出书籍为我们想要的格式，比如我很喜欢的 `epub` 格式，因为可以直接在苹果手机自带的图书 `APP` 查看。

命令如下
```shell
$ gitbook epub #输出为 epub 格式
$ gitbook pdf  #输出为 pdf 格式
$ gitbook mobi #输出为 mobi 格式
```

导出书籍时最好再在项目的根目录下添加一个 `book.json` 文件，标明书名和一些其他信息，具体可以看 [这里](http://www.chengweiyang.cn/gitbook/customize/book.json.html)

```shell
$ cat book.json
{// Book metadats (somes are extracted from the README by default)
    "title": "testbook"
}
```

### 发布

发布的选择有很多种，可以通过 [gitbook.com](gitbook.com)、[GitHub pages](https://pages.github.com/)、[看云](https://www.kancloud.cn/) （一个国内的免费平台）。

### 更多

更多指令可以通过运行

```shell
$ gitbook help
```

### 已知问题

如果你没有安装 12.0 以下版本的 node，那么你是没法使用自动重载功能的。