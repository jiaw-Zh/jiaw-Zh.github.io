---
title: "部署一个随时可用的在线 VS Code"
date: 2023-08-09T23:37:26+08:00
categories: ['工具']
tags:  ['2023']
draft: false
---

## 安装

### 使用命令安装
如果网络好的话直接一条命令下载（国内服务器应该都不行）

```curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run```

然后安装和运行

```curl -fsSL https://code-server.dev/install.sh | sh```

### 手动安装
从这里下载，我是 ubuntu，选择的是  `code-server_4.16.1_amd64.deb`

https://github.com/coder/code-server/releases

使用 sftp 工具上传到服务器上，然后执行

`sudo dpkg -i code-server_4.16.1_amd64.deb`

启动 `systemctl enable --now code-server@root`

配置文件在 `~/.config/code-server/config.yaml`

文件内容：
```yaml
bind-addr: 127.0.0.1:8080
auth: password
password: dsafsgdhdtr3436
cert: false
```

如果你想直接通过IP访问，需要修改 `bind-addr` 的 `127.0.0.1` 为 `0.0.0.0`

因为我使用了 caddy 进行反代，所以这里还是`127.0.0.1`


### Windows 使用
使用npm方式安装

https://coder.com/docs/code-server/latest/npm#windows