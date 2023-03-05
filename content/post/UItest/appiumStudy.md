---
title: "Appium 学习笔记"
date: 2022-12-13T15:18:15+08:00
draft: false
categories: [UI自动化]
tags: ["2022","自动化测试","未完成"]
lastmod: 2023-03-05
---

### Appium 生态工具介绍
- Appium Desktop：内嵌了 Appium Server 和 Inspector 的综合工具
- Appium Server：Appium 的核心工具，命令行工具
- Appium Clients：各种语言的客户端封装库，用于连接 appium server
    - Java、Python、Ruby、JavaScript、C#、robotframework

### Appium 的安装

以下所有文件我都放在了我的云盘里 https://www.aliyundrive.com/s/6LxYYQxJjDA

我选择 Appium Desktop + Appium Inspector + Python 的 Clients（在无 GUI 的服务器运行时选择[npm方式安装](https://github.com/appium/appium#server)）
- https://github.com/appium/appium-desktop/releases
- https://github.com/appium/appium-inspector/releases
- ```pip install Appium-Python-Client```

想要使用 Appium 还需要Java 和 adb 环境，Java不多讲，说一下 adb 的环境配置：

下载 platform-tools ，放到一个文件夹中，比如我的是 `D:\Program\adb`，然后把这个路径添加到环境变量中，
并将 appuim 的 ANDROID_HOME 设置为上面的路径，最后把 `apksigner.jar` 放入上面的目录。

`apksigner.jar` 来之不易，记录一下经过：
- 在 https://developer.android.com/studio 下载 Command line tools only
- 把压缩包内的所有文件放到新建的 latest 文件夹下
- 在latest文件夹下执行 `.\bin\sdkmanager.bat --channel=0 --install "build-tools;30.0.2"` 安装Build-Tools
- 在 `<adb>\build-tools\30.0.2\lib` 目录下找到 `apksigner.jar`

### Appium Desktop 的主要功能
- UI 分析
- 录制用例
- 元素查找测试
- Attach 已有的 session
- 云测试


### Appium Inspector 的使用
1. 打开 Appium Server GUI -> Advanced;Server address: localhost;Port: 4723;Allow CORP: yes

2. 打开 Appium Inspector;Remote host: localhost;Port: 4723;Path: /wd/hub

3. 勾选 Allow Unauthorized Certificates

4. 选择自己的配置

5. 启动服务

### Appium Caps

```python
caps["autoGrantPermissions"] = True  # 获取需要的权限
caps["appPackage"] = "com.xueqiu.android" #应用包名，获取方法在下面
caps["appActivity"] = ".view.WelcomeActivityAlias" #期望在应用内启动的活动页，一般以 . 开头
```

获取应用包名和activity
```powershell
#打开应用并停留在想获取的 activity 页面，执行下面的命令
$ adb shell dumpsys window | findstr mCurrentFocus
  mCurrentFocus=Window{efa5b65 u0 com.xueqiu.android/com.xueqiu.android.view.WelcomeActivityAlias}
```
`com.xueqiu.android/com.xueqiu.android.view.WelcomeActivityAlias` 中
`com.xueqiu.android` 就是应用包名
`.view.WelcomeActivityAlias` 就是当前页面的 activity
