---
title: "中国移动RAX3000Q路由器安装 Luci"
date: 2022-09-07T20:55:10+08:00
draft: false
categories: ['其他']
tags: ["2022",'openwrt']
---

> 本文基于软件版本：V1.1.1.202204141725，硬件版本 TZ7.823.346A ，MAC地址: 04:4f开头的中国移动 RAX3000Q 路由器

> 如果你装 luci 是为了安装 luci-app-v2ray，我的建议是直接放弃(存储空间不足外加一堆问题)

> 本文默认你已可以通过 ssh 连接到 openwrt，如果还不知道怎么连，看我[上一篇文章]({{< ref "/content/post/other/rax3000q-ssh-openwrt.md">}})

## 为什么要手动安装

所谓道高一尺魔高一尺，自从有大佬发现这货自动一个 luci，并曝光之后，在后续版本 luci 就被清理干净了，所以我们来手动安装一下

## 更换相近架构

*本部分直接引用大佬[文章](https://blog.imlk.top/posts/rax3000q-get-shell/)中的“继续探索部分”*

IPQ5018 是双核 Cortex-A53 处理器，opkg 默认的架构为 ipq，但是 OpenWrt 软件源里并没有这种架构，我们需要选择一个相近的架构。

根据 cpuinfo 信息判断应该是 armv7l 且支持 vfpv4 硬件浮点，但是内置的 `interpreter` 却是 `/lib/ld-musl-arm.so.1`，只支持软件浮点，这个问题可以创建一个软链接来解决。

```shell
ln -s /lib/ld-musl-arm.so.1 /lib/ld-musl-armhf.so.1
```

由于目前运行的QSDK是32位的，无法像这篇文章一样直接使用 `aarch64_cortex-a53` 的软件源，最终我选择的架构是 `arm_cortex-a7_neon-vfpv4`。

在 /etc/opkg.conf 中添加以下内容：

```
arch all 1
arch noarch 1
arch ipq 10
arch arm_cortex-a7_neon-vfpv4 20
```

在探索过程中我发现 iqp40xx 处理器的包也是可以用，架构和我们修改后的一致

地址如下：[https://downloads.openwrt.org/releases/18.06.0/targets/ipq40xx/generic/packages/](https://downloads.openwrt.org/releases/18.06.0/targets/ipq40xx/generic/packages/)



## 开始安装

opkg 的一般用法是先 `opkg update` 再 `opkg install 包名`，但是我们的系统版本比较低，`wget` 版本也低，不支持 `https`，所以我们选择使用 `opkg install + 包的地址`

按顺序执行下面的命令，每次一条，应该不会报错，依赖关系都理好了

```
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libjson-script20210516_2021-05-16-b14c4688-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/uhttpd_2021-03-21-15346de8-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/base/lua_5.1.5-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-lib-nixio_git-20.356.64372-1259bb1-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-lib-ip_git-20.356.64372-1259bb1-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-lib-jsonc_git-20.356.64372-1259bb1-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/liblucihttp_2019-07-05-a34a17d5-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/liblucihttp-lua_2019-07-05-a34a17d5-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-base_git-20.356.64372-1259bb1-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-mod-admin-full_git-20.356.64372-1259bb1-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-theme-bootstrap_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-proto-ppp_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libnl-tiny1_2020-08-05-c291088f-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libubox20210516_2021-05-16-b14c4688-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libuci20130104_2021-04-14-4b3db117-5_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libubus20210630_2021-06-30-4fc532c8-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libiwinfo-data_2022-04-26-dc6847eb-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libiwinfo20210430_2022-04-26-dc6847eb-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/liblua5.1.5_5.1.5-9_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libiwinfo-lua_2022-04-26-dc6847eb-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-proto-ipv6_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/rpcd-mod-rrdns_20170710_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-app-firewall_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/luci/luci-app-opkg_git-22.154.41894-1cf976c_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-proto-ppp_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libjson-c5_0.15-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/libblobmsg-json20210516_2021-05-16-b14c4688-2_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/packages-21.02/arm_cortex-a7_neon-vfpv4/base/rpcd_2022-02-19-8d26a1ba-1_arm_cortex-a7_neon-vfpv4.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-i18n-base-zh-cn_git-20.356.64372-1259bb1-1_all.ipk
opkg install http://mirrors.tuna.tsinghua.edu.cn/openwrt/releases/18.06.0/packages/arm_cortex-a7_neon-vfpv4/luci/luci-theme-material_git-20.356.64372-1259bb1-1_all.ipk
```

上面的命令都成功执行之后，再执行下面的命令：

```
uci -q delete uhttpd.main.listen_http
uci add_list uhttpd.main.listen_http="0.0.0.0:8080"
uci add_list uhttpd.main.listen_http="[::]:8080"
uci commit uhttpd
```


设置服务开机自启以及启动服务：

```
/etc/init.d/rpcd enable
/etc/init.d/uhttpd enable
/etc/init.d/rpcd start
/etc/init.d/uhttpd start
```

rpcd 的配置文件中，关于 ubus 的配置**可能**多了一些信息，需要删掉，修改后的文件：
```
vi /etc/config/rpcd
config rpcd
	option socket /var/run/ubus.sock
	option timeout 30
# 下面的内容不要改
```
## enjoy~

现在访问 [http://192.168.10.1:8080](http://192.168.10.1:8080) 即可见到 luci