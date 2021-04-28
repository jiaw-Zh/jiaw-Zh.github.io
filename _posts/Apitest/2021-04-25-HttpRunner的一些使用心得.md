---
layout: post
title:  "HttpRunner 的一些使用心得"
date:   2021-04-25 10:52:00 +0800
categories: ApiTest httprunner
tags: ApiTest 2021
toc: true
---

## 简介
<br>
<a href="https://github.com/httprunner/httprunner" target="_blank">HttpRunner</a>
 is a simple & elegant, yet powerful HTTP(S) testing framework.

本文主要记录特定版本： **v==3.0.1** 的相关使用心得。

----
## 版本选择
<br>
当前最新版本是 **3.1.4** ，该版本作者推荐使用 **.py** 文件进行脚本编写和维护，主要是为了复用 IDE 的链式调用功能，
不过个人并不喜欢，同时新版本在兼容旧版本脚本方面存在一定问题，迁移工作量较大。

因此个人还是推荐使用 **3.0.1** 版本，这个版本和 **2.0.x** 版本使用很接近，因此我们在使用中可以看一下 **2.0.x** 的
<a href="https://v2.httprunner.org/" target="_blank">文档</a>。

**3.0.x** 的官方文档在<a href="https://docs.httprunner.org/" target="_blank"> 这里</a>。

同时，这里提供一份他人翻译的<a href="https://www.ontheway.cool/HttpRunner3DocsForCN/" target="_blank"> 中文文档</a>，
个人觉得还是写的挺好，不仅仅是翻译。

----
## 安装
<br>

```shell
$pip install httprunner==3.0.1 -i https://mirrors.aliyun.com/pypi/simple/
```

鉴于国内的网络环境，因此我们使用一个 **-i** 参数来指定 pypi 源。

----
## 创建一个 demo
<br>
使用如下命令：

```shell
$httprunner --startproject demo 
# 官方文档startproject前是不需要加--的
# 但是-h参数表示需要加，不加也的确会报错
httprunner.utils:create_scaffold:359 - Start to create new project: demo
httprunner.utils:create_folder:365 - created folder: demo
httprunner.utils:create_folder:365 - created folder: demo\api
httprunner.utils:create_folder:365 - created folder: demo\testcases
httprunner.utils:create_folder:365 - created folder: demo\testsuites
httprunner.utils:create_folder:365 - created folder: demo\reports
httprunner.utils:create_file:371 - created file: demo\api\demo_api.yml
httprunner.utils:create_file:371 - created file: demo\testcases\demo_testcase.yml
httprunner.utils:create_file:371 - created file: demo\testsuites\demo_testsuite.yml
httprunner.utils:create_file:371 - created file: demo\debugtalk.py
httprunner.utils:create_file:371 - created file: demo\.env
httprunner.utils:create_file:371 - created file: demo\.gitignore
```

生成的文件目录结构：

<img src='/img/httprunner/demo_tree.png' width="auto" height="auto">

从上面的目录结构能够很清楚看出来，这个版本的 HttpRunner 分了 **3** 层：api 层、testcase 层、testsuite 层。

> ps.最新版本推荐分两层，个人觉得也可以接受
> 
> 因为设定了 testcase 里可以调 testcase
> 
> 感兴趣可以建一个最新版的 demo 看下

---
## mock 一个接口用于测试

接口文档：


---
## 编写脚本
根据上面的接口文档，我们可以写出如下脚本

```shell
$cat api/demo_api.yml
name: demo api
variables:
    body:
        name: $name
        age: $age
        address: $address
        salary: $salary
request:
    url: /insert
    method: POST
    headers:
        Content-Type: "application/json"
    json: $body
validate:
    - eq: ["status_code", 200]
```




---
## 变量作用域
<br>
在每一层都可以定义变量，但是各层是有不同的权重的，api层拥有最高的权重，即如果你已经在
api 层定义了一个变量，那么之后除非你在 testcase 层是对

---
## 参数化
<br>


---
## 自定义函数
<br>


<br>