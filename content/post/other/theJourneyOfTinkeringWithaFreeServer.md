---
title: "一台免费服务器的折腾之路"
date: 2023-07-15T17:37:22+08:00
draft: false
categories: ['其他']
tags: ["2023",'服务器']
---

从 AWS 薅了一台免费服务器，单核 1Gb 内存，先搭一个梯子

**这里有一个大坑，记得把防火墙所有端口入站都打开**

### 安装 BBR 加速

```shell
sudo su root
wget -O tcp.sh "https://github.com/ylx2016/Linux-NetSpeed/raw/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```
- 安装 BBR 原版内核 -1-11
- 安装 BBRplus 新版内核 -5-19

## 安装代理

我选择的是 [shadowsocks-rust]，选择的原因就两个，首先rust写的，性能应该不错，然后支持[v2ray-plugin]

### 下载 shadowsocks-rust

```shell
cd /usr/local/bin/
# 下载
wget https://github.com/shadowsocks/shadowsocks-rust/releases/download/v1.15.4/shadowsocks-v1.15.4.x86_64-unknown-linux-gnu.tar.xz
# 解压
tar xvf shadowsocks-v1.15.4.x86_64-unknown-linux-gnu.tar.xz
```
### 创建配置文件
```shell
# 创建目录
mkdir /etc/shadowsocks-rust && cd /etc/shadowsocks-rust

vim config.json

# 配置内容
{
"server": "0.0.0.0",
"server_port": 59876,
"timeout": 600,
"method": "aes-128-gcm",
"password": "1a2b3c"
}
```

### 验证是否能够启动
```shell
临时运行，检测ss是否能够启动
/usr/local/bin/ssserver -c /etc/shadowsocks-rust/config.json
```

### 创建服务文件service
```shell
# 切换路径
cd /etc/systemd/system/ && vim ssrust.service

# 内容
[Unit]
Description=Shadowsocks-Rust Service
After=network.target

[Service]
Type=simple
User=nobody
Group=nogroup
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks-rust/config.json

[Install]
WantedBy=multi-user.target
```
### 配置systemd
```shell
systemctl enable ssrust //加入开机自启
systemctl disable ssrust //取消开机自启
systemctl start ssrust //启动
systemctl stop ssrust //停止
systemctl is-enabled ssrust //判断服务是否处于开机自启状态，输出enabled即代表开机自启
```
## 防火墙
根据这篇文章：[如何部署一台抗封锁的Shadowsocks-libev服务器](https://gfw.report/blog/ss_tutorial/zh/)，我也使用ufw配置防火墙简单加固一下

在 Debian 11 安装 ufw
```shell
sudo apt update && sudo apt install -y ufw
```

开放有关 ssh 和 Shadowsocks-libev 的端口。

```shell
sudo ufw allow ssh
sudo ufw allow 8388  //8388替换为你的ss服务端口
```

启动 ufw
```shell
sudo ufw enable
```

使用```sudo ufw status```检查配置是否成功：

```shell
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
8388                       ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
8388 (v6)                  ALLOW       Anywhere (v6)
```


[shadowsocks-rust]:https://github.com/shadowsocks/shadowsocks-rust
[v2ray-plugin]:https://github.com/shadowsocks/v2ray-plugin