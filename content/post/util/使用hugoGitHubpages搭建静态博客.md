---
title: "使用 Hugo + GitHub Pages 搭建免费静态博客"
date: 2021-11-18T10:47:42+08:00
categories: ['工具']
tags:  ['2021','hugo','github']
draft: false
---

> 告别 Jekyll，使用 Hugo 加 GitHub Pages 搭建一个免费静态博客吧！

因为经常在浏览博客的时候看到博客的地址是 `github.io` 后缀，出于好奇心就打开这个网址看了下，这是与 `Github Pages` 的初次相遇。

也刚好当时在准备跳槽，所以就顺手搭建了一个静态博客，写一些文章。

`Github Pages`推荐的博客生成工具是 `jekyll` ，所以一开始我也是选择了它，但是使用过程着实有些一言难尽，所以我就换了一个，这次是 `Hugo` 。

## Hugo 的安装与基本使用

关于 [Hugo](https://gohugo.io/) 的安装，可以直接看[官方文档](https://gohugo.io/getting-started/quick-start/)，也可以看这篇官方的[翻译文章](https://www.gohugo.org/)

但是为什么已经有了上面的两个链接，我还要写这篇文章呢？当然不是为了水文，重点在于与 `GitHub Pages` 的配合上

**使用 Hugo 的主要流程**

- 安装 Hugo 主程序
    - 推荐二进制安装，不需要 go 语言环境（这里看[官方安装文档](https://gohugo.io/getting-started/installing/)比较好）
    - 关键点就是将下载的二进制文件加入环境变量
    - 安装完成后，在终端执行 `hugo version` 查看版本号
- 创建站点
    - 在终端执行 `hugo new mysite` 创建一个新的站点
- 添加主题
    - 主题可能会有自己的配置要求，这个主要看你选择什么主题，我选择了 [even](https://github.com/olOwOlo/hugo-theme-even) 主题
- 创建文章
    - 在终端执行 `hugo new posts/my-first-post.md` 创建一个新的文章
    - 可能你的主题会有不同的要求，比如我的主题就要求文章需要放在`post`目录下，所以我在终端执行 `hugo new post/my-first-post.md` 创建一个文章
- 生成与运行站点
    - 在终端执行 `hugo` 命令，会在当前目录生成一个 `public` 目录，这个目录下的文件就是站点的静态文件
    - 在终端执行 `hugo server` 命令，就会启动 `Hugo` 服务，访问 [http://localhost:1313/](http://localhost:1313/) 就会看到你的站点

上面的翻译文章与我的流程还是有一点出入的。

## 配合 GitHub Pages 发布博客

到我的重头戏咯，无论是[官方文档](https://gohugo.io/getting-started/usage/#deploy-your-website)还是翻译文档，他们与 `GitHub Pages` 配合的主要方式都是将 `pubilc` 目录里所有文件 push 到 `GitHub` 上，但是这就会有一个问题，你无法在任何地方更新文章了。

**所以这里我的做法就是：**

- 创建一个仓库
    - 仓库命名为：`coderzh.github.io` （`coderzh` 替换为你的 `github` 用户名）
- 创建一个 `gh-pages` 分支
    - 将 `public` 目录里的文件 `push` 到 `gh-pages` 分支上

