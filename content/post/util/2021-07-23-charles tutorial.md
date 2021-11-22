---
title:  "Windows 版 Charles 简明使用教程"
date:   2021-07-23
lastmod: 2021-11-22
categories: ['工具']
tags:  ['2021',Charles']
---

> 和 `fiddler` 同样强大、全能的抓包工具

## 主要特色

- 支持多操作系统
- 网络抓包（包括 `HTTPS` )
- 请求重定向

## 安装

去 <a href='https://www.charlesproxy.com/download/' target="_blank">官方下载页</a> 根据你的操作系统下载对应的安装包，然后一路下一步完成安装即可。

**小彩蛋：** 获取专业版 Charles

<a href='https://www.zzzmode.com/mytools/charles/' target="_blank">方法1</a>

<a href='https://goplay.space/#3K2iuH9cREz' target="_blank">方法2</a>

<a href='https://goplay.tools/snippet/3K2iuH9cREz' target="_blank">方法3</a>

方法2和3需要修改第19行的用户名，然后点击 run

## 使用

### 安装证书

1. 顶部菜单 Help -> SSL Proxying -> Save Charles Root Certificate...

2. 在文件名后面填上证明名称，比如我写的 ssl，文件类型选择 pem，然后点击保存。

    <img src='/img/charles/Charles.png' alt='保存根证书'>

3. 设置浏览器（以 chrome 为例）
   1. 打开设置 -> 隐私设置和安全性 -> 安全 -> 管理证书
   2. 点击导入，选择上一步保存的证书
   3. 选择将所有的证书都放入下列存储：受信任的根证书颁发机构
   4. 一直下一步即可

### 查看本机局域网地址

顶部菜单 Help -> Local IP Address

如果有多个地址，注意根据网络接口名称区分。

### 设置抓取 HTTPS 请求

顶部菜单 Proxy -> SSL Proxy Settings

然后根据图片设置

<img src='/img/charles/Charles2.png' alt='Charles 设置'>

现在已经可以抓取 PC 端浏览器的请求了。


### 手机抓包设置

**前提：手机和电脑在同一局域网！！！**

顶部菜单 Help -> SSL Proxying -> Install Charles Root Certificate On a Mobile Device...

然后根据提示在手机上设置代理：
- iOS 
  - 点击WiFi -> 配置代理 -> 输入电脑的 IP 和端口
- 安卓
  - 长按已连接的 WiFi -> 高级设置 -> 配置代理 -> 输入电脑的 IP 和端口

设置好代理之后在浏览器访问 `chls.pro/ssl` 下载证书进行安装。

代理的端口是可以更改的，修改路径为：顶部菜单 Proxy -> Proxy Settings，默认端口是 8888

**iOS设备需要多一步设置：**

在设置 -> 通用 -> 关于本机 -> 证书信任设置中，开启对 Charles 证书的信任。


### 过滤请求

这个比较简单，就是在页面左下角有一个 `Filter` 输入框，可以用来过滤你想看到的请求。

### 模拟弱网

这个也比较简单，而且使用上比 `fiddler` 要简单很多

设置方法：顶部菜单 Proxy -> Throttle Setting

设置之后快捷开关键是那个小乌龟

开启弱网：

<img src='/img/charles/Charles3.png' alt='开启弱网'>

关闭弱网：

<img src='/img/charles/Charles4.png' alt='关闭弱网'>


### 请求重定向

这里的有两个选择: Map Remote 、Map Local。

Map Remote：将请求重定向到另一个网址，设置方法：顶部菜单 Tools -> Map Remote

Map Local：将请求重定向到本地文件，设置方法：顶部菜单 Tools -> Map Local

这两个功能挺好用的，但是基本打开页面就知道该怎么用了，所以也不做细讲。（如果你不会用，说明对于 HTTP 请求、响应的理解不够深入）
