---
title: "真离线安装 Shellclash 与使用"
date: 2022-09-07T22:32:54+08:00
draft: false
categories: ['其他']
tags: ["2022",'openwrt']
---

> 路由器连接方式别选择桥接模式，有坑

看了作者写的本地安装方式我表示还不够离线，通过查看安转脚本，重新整理了一下本地安装过程

## 准备环境

首先你需要一个 ssh 环境

其次还要在本地假设一个文件服务器，我更推荐 [chfs](http://iscute.cn/chfs)

当然如果你用的不多，又有 `Python` 环境，也可以一行命令完成： `python -m http.server`

## 下载与修改安装文件

可用的服务器列表: `url_cdn`

```
https://raw.fastgit.org/juewuy/ShellClash
https://raw.githubusercontents.com/juewuy/ShellClash
```

1. 下载[安装脚本](https://raw.githubusercontent.com/juewuy/ShellClash/master/install.sh) （保存为 `install.sh` 文件）
2. 查看当前[最新版本](https://raw.githubusercontents.com/juewuy/ShellClash/master/bin/release_version)，得到 `release_new`
3. 拼接安装程序地址: `url_cdn/release_new/bin/clashfm.tar.gz`

    拼接好的 1.6.3 版本

    ```
    https://raw.fastgit.org/juewuy/ShellClash/1.6.3/bin/clashfm.tar.gz
    https://raw.githubusercontents.com/juewuy/ShellClash/1.6.3/bin/clashfm.tar.gz
    ```
4. 修改 install.sh 文件

    文件第 57 行 `#检查更新` 至 82 行 `rm -rf /tmp/clashrelease` 全部删除
    
    第 83行 `tarurl=$url_dl/bin/clashfm.tar.gz` 修改为 `tarurl=http://192.168.10.28/chfs/shared/clashfm.tar.gz`

    即将 tarurl 的值修改为本地文件服务器(使用chfs)中安装程序所在路径

5. 修改执行命令

    官方的安装脚本如下，我们需要修改脚本中的 url：

    ```
    export url='http://shellclash.cf/' && wget -q -O /tmp/install.sh $url/install.sh  && sh /tmp/install.sh && source /etc/profile &> /dev/null
    ```

    本地文件服务器地址(使用chfs)：`http://192.168.10.28/chfs/shared`

    替换后得到：

    ```
    export url='http://192.168.10.28/chfs/shared' && wget -q -O /tmp/install.sh $url/install.sh  && sh /tmp/install.sh && source /etc/profile &> /dev/null
    ```

## 安装与配置

以下操作均在需要安装 shellclash 的机器上进行

执行我们在上一步替换后得到的命令，没有意外的话就可以看到 shellclash 的管理页面了

根据程序提示选择适合自己的选项即可

### 程序配置

到这里还没有结束，因为我们修改了安装脚本，所以还有一个地方需要修改

在 shellclash 管理脚本选择 `9 更新/卸载` >> `7 切换安装源及安装版本`

在这个页面我们可以看到当前的源是我们修改的执行命令中的本地文件服务器地址，选择一个可用的源即可

如果你的 wget 版本比较低，不支持 https，选择 `8 自定义源地址(用于本地源或自建源)`

然后输入：`http://gh.shellclash.cf/master`


### 账号配置

此部分同样是为 wget 版本比较低，不支持 https 的用户而写。

在 shellclash 管理脚本选择 `6 导入配置文件` >> `1 在线生成Clash配置文件` 

输入 `vemss:....` 链接回车

再次选择 `1 在线生成Clash配置文件` 

正常会开始报错：

```
正在连接服务器获取配置文件…………链接地址为：
https://sub.xeton.dev/sub?target=clash&insert=true&new_name=true&scv=true&udp=true&exclude=&include=&url=vmess://ew0KICAidiI6ICIyIiwNCiAgInBzIjogIjIwMjMt9yayIsDQogICJwb3J0IjogIjQ0MyIsDQogICJpZCI6ICI5MjNkMzc2Mi1jMDFhLTQwMjgtYjM2Ny1jMzAwYjI2YzU2OTgiLA0KICAiYWlkIjogIjMyIiwNCiAgIm5ldCI6ICJ3cyIsDQogICJ0eXBlIjogIm5vbmUiLA0KICAiaG9zdCI6ICIiLA0KICAicGF0aCI6ICIvc2VwIiwNCiAgInRscyI6ICJ0bHMiDQp9&config=https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/lhie1_dler.ini
可以手动复制该链接到浏览器打开并查看数据是否正常！
Connecting to sub.xeton.dev (104.21.233.209:443)
wget: error getting response: Connection reset by peer
Connecting to sub.xeton.dev (104.21.233.210:443)
wget: error getting response: Connection reset by peer
sh: out of range
配置文件获取失败！
```

复制上面报错信息中的 https 链接，用本地浏览器打开，另存为 `config.yaml`，同样放在本地文件服务器目录下

回到 shellclash 管理脚本，选择`6 导入配置文件` >> `2 导入Clash配置文件链接`  

输入上面保存的 `config.yaml` 文件地址回车

重启 shellclash

一起 OK 啦~