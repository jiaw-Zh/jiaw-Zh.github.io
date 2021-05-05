---
layout: post
title:  "使用 brew services 管理服务"
date:   2021-03-15
categories: homebrew 
tags: homebrew 2021
---

> 此命令仅使用是 MacOS

官方关于 **brew services** 命令的说明在
<a href='https://docs.brew.sh/Manpage#services-subcommand' target="_blank">这里</a>

**macOS** 使用 **launchctl** 命令加载开机自动运行的服务，**brew services** 简化了 **lauchctl** 的操作，
并因此 **brew services** 命令是不可以在 **Linux** 下使用的。

### 常用命令

```shell
brew services list  # 查看使用brew安装的服务列表
brew services run (formula|--all)  # 启动服务（仅启动不注册）
brew services start (formula|--all)  # 启动服务，并注册
brew services stop (formula|--all)   # 停止服务，并取消注册
brew services restart (formula|--all)  # 重启服务，并注册
brew services cleanup  # 清除已卸载应用的无用的配置
```

此处注册即指设置开机自启

### 示例

启动 MySQL 并停止

```shell
$brew services list # 查看使用brew安装的服务列表
Name        Status  User    Plist
caddy       stopped
jenkins-lts stopped
mysql@5.7   stopped

$brew services run mysql@5.7 # 启动 mysql 服务（仅启动不注册）
==> Successfully ran `mysql@5.7` (label: homebrew.mxcl.mysql@5.7)

$brew services stop mysql@5.7 # 停止服务
Stopping `mysql@5.7`... (might take a while)
```
