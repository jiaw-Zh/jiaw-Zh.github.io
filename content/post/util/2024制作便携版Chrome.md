---
title: "自己制作便携版 Chrome"
date: 2024-02-27
draft: false
categories: ['工具']
tags:  ['2024']
---

### 原理

利用GoogleChromePortable.exe启动器来启动Chrome主程序，所有Chrome用户数据都指向本程序所在的Data目录，从而实现和系统安装的Chrome隔离。

### 获取 GoogleChromePortable.exe

在 https://portableapps.com/apps/internet/google_chrome_portable 下载最新版，不要安装，用7-Zip打开这个压缩包，根目录下面有一个GoogleChromePortable.exe文件，提取出来，这个文件就是我们需要的启动器。

### 提取Chrome主程序

在 https://www.iplaysoft.com/tools/chrome/ 下载 Chrome 最新离线安装包

离线安装包下载好后，不要运行，我们同样用7-Zip打开这个压缩包，会发现里面有一个 *chrome.7z* 文件，我们把他提取出来。

### 开始制作便携版


- 新建一个文件夹，用来存放便携版，自行更名，比如：*chrome portable*
- 复制 *GoogleChromePortable.exe* 到这个文件夹，可以改名成自己想要的名称，不改也可以
- 新建 App 文件夹，把 *chrome.7z* 解压到这个目录内，注意只要Chrome-bin文件夹，完成后的目录结构应该是 */chrome portable/App/Chrome-bin*

这样就完成制作了，非常简单。双击GoogleChromePortable.exe就能启动这个Chrome了