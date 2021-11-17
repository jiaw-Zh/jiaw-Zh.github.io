---
title:  "为 Windows PowerShell 设置 Alias （命令别名）"
date:   2021-06-15T16:25:00+08:00
categories: ['操作系统']
tags:  ['2021','alias']
---

> alias 用的好真的可以为所欲为啊

# 啰里啰唆可以直接跳过的一段

最近黑苹果挂了，暂时用回 Windows，发现想要在 Windows 用 alias 还有点难度，
网上搜索下来大多数都是关于 cmd 下的方法，作为一个紧跟潮流的测试人，
当然要用 PowerShell 啦！

一番百度之下(这个习惯很不好，尽量谷歌~)，竟然也成功找到了相关设置方法。

ps.其实是因为我直接搜的：Windows下alias的配置，关键字不对害死人啊

so.本文主要内容转载自 
<a href='https://blog.vvzero.com/2019/07/22/set-user-alias-for-windows-PowerShell/' target="_blank"> blog.vvzero.com </a>

特别巧的是我一开始也是用的 `cmder`，然后启动时间太长无法忍受，无意中结识 `Windows Terminal `，
深得我心，微软大法好啊。

# 正文开始

## 探索过程及原理概述

如果搜索关键词 `windows powershell set user alias`，通常谷歌会给出<a href='https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias?view=powershell-6' target='_blank'>微软官方文档</a>，但是这个文档只是告诉我们如何在脚本里面设置临时的别名，如果要设置永久别名，该怎么办？实际上，“别名”这种东西，也就是 `alias`，几乎所有的脚本语言，都没有所谓的“永久别名”(Permanent alias)，我们使用 Linux bash 、Cmder 之类的脚本工具，打开终端时，系统会默认执行一个脚本文件（ bash 是用户目录下的 `.bashrc`，Cmder 是 `config/user_aliases.cmd` ），而这样的脚本文件里，就包含了别名的定义。这也是为什么，我们在 Linux 类系统中，修改 `.bashrc` 后，必须要重新登出登录、或者 `source .bashrc` 的原因了。

所以，我们只要修改 `Windows Powershell` 启动时执行的文件就行了。很多论坛里面说，默认执行的脚本是 `$Home\Documents\profile.ps1` ，也就是 `C:\Users\你的用户名\Documents\profile.ps1` ，但是这并不正确，最好的方式是，先启动 `PowerShell` ，再执行 `echo $profile`，这样得到的文件路径，才是 `PowerShell` 的默认执行文件路径。

```
PS D:\Code> echo $profile
C:\Users\username\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

然后，创建这个文件就好啦。

在文件里面，写上别名设置的语句。再一次注意，假如你的别名指代的命令含有空格，就不可以使用 `New-Alias` 命令，因为它不能带空格，即使你把指代的命令用引号括起来也没用。那怎么办呢？继续谷歌，原来，正确姿势是用 `function` ，也就是，我们把自己要定义的指令，定义为一个函数，就行啦。

```
# function 别名{原命令}
function blog{bundler exec jekyll s}
```

保存文件，重新启动 PowerShell 以后，不出意外，应该会报一个 `File xxxxxxx\Microsoft.PowerShell_profile.ps1 cannot be loaded because running scripts is disabled on this system.` 
根据 <a href='https://tecadmin.net/powershell-running-scripts-is-disabled-system/' target='_blank'>此链接</a>，出现这种情况，是因为 Windows 系统为了防止恶意脚本自动执行，故默认不允许自动运行脚本。所以，在确定自己有能力把控的情况下，__以管理员身份__，在 `PowerShell` 中执行 `Set-ExecutionPolicy RemoteSigned` ，即可。

再次重启 `PowerShell` ，应该可以发现，自定义别名已经生效了。

# 最后总结

如果你想为自己的 Windows PowerShell 设置永久的命令别名 (Alias)，可以按以下步骤设置：
- 打开 `PowerShell` ，运行 `echo $profile`，会输出一个文件路径。创建这个文件。
- 打开刚创建的文件，按以下格式设置多条别名：`function 别名 { 原命令 }`
- 以管理员身份打开 `PowerShell`，执行 `Set-ExecutionPolicy RemoteSigned`。
- 重启 `PowerShell` ，就可以愉快的玩耍啦。