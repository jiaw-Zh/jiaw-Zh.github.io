---
layout: post
title:  "使用 HttpRunner 时一些值得关注的点"
date:   2021-04-25 10:52:00 +0800
categories: ApiTest httprunner
tags: ApiTest 2021 httprunner
toc: true
---

<br>
> <a href="https://github.com/httprunner/httprunner" target="_blank">HttpRunner</a>
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
<br>

接口文档：
```
host: http://127.0.0.1:8000
method: post
url: /insert
请求参数：
{
  "name": "string",
  "age": 0,
  "address": "string",
  "salary": 0
}

响应：
{
  "success": true,
  "msg": "msg"
}
```

---
## 编写脚本
<br>
根据上面的接口文档，我们可以写出如下脚本

api 层：
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

testcase 层：
```shell
config:
    name: "demo testcase"
    base_url: "http://127.0.0.1:8000"

teststeps:
-
    name: demo testcase
    api: api/demo_api.yml
    variables:
        name: noel
        age: 15
        address: beijing
        salary: 7777
    extract:
        - msg: content.msg
    validate:
        - eq: ["status_code", 200]
```

testsuite 层：
```shell
config:
    name: "demo testsuite"
    base_url: "http://127.0.0.1:5000"

testcases:
-
    name: call demo with api data
    testcase: testcases/demo_testcase.yml
```
---

## 执行一下脚本
<br>

```shell
$hrun testsuites/demo_testsuite.yml
2021-04-28 23:16:37.317 | INFO     | httprunner.api:run:334 - HttpRunner version: 3.0.1
2021-04-28 23:16:37.318 | INFO     | httprunner.loader.load:load_dot_env_file:172 - Loading environment variables from /Users/jiawang/PycharmProjects/oldhttprunner/demo/.env
2021-04-28 23:16:37.343 | INFO     | httprunner.api:_run_suite:146 - Start to run testcase: call demo with api data
2021-04-28 23:16:37.343 | INFO     | httprunner.report.html.result:startTest:30 - demo testcase
2021-04-28 23:16:37.344 | INFO     | httprunner.runner:_run_test:242 - POST http://127.0.0.1:8000/insert
2021-04-28 23:16:37.350 | INFO     | httprunner.client:request:221 - status_code: 200, response_time(ms): 6.15 ms, response_length: 79 bytes

.

----------------------------------------------------------------------
Ran 1 test in 0.007s

OK
2021-04-28 23:16:37.363 | INFO     | httprunner.report.html.gen_report:gen_html_report:34 - Start to render Html report ...
2021-04-28 23:16:37.396 | INFO     | httprunner.report.html.gen_report:gen_html_report:61 - Generated Html report: /Users/jiawang/PycharmProjects/oldhttprunner/demo/reports/20210428T151637.343656.html

```
贴一下测试报告里的请求与响应
```
Request:
url	http://127.0.0.1:8000/insert
method	POST
headers	
{
  "User-Agent": "python-requests/2.25.1",
  "Accept-Encoding": "gzip, deflate",
  "Accept": "*/*",
  "Connection": "keep-alive",
  "Content-Type": "application/json",
  "HRUN-Request-ID": "4d2a31e2-481d-425d-8718-540aa8b342bb",
  "Content-Length": "65"
}
body	
{
  "name": "noel",
  "age": 15,
  "address": "beijing",
  "salary": 7777
}

Response:
ok	True
url	http://127.0.0.1:8000/insert
status_code	200
reason	OK
cookies	{}
encoding	utf-8
headers	
{
  "date": "Wed, 28 Apr 2021 15:16:36 GMT",
  "server": "uvicorn",
  "content-length": "79",
  "content-type": "application/json"
}
content_type	application/json
body	
{
  "success": true,
  "msg": "此人名字叫做：noel，十年后此人年龄：25"
}
```




---
## 变量权重（重点）
<br>


在每一层都可以定义变量，但是各层的权重是不同的。api 层拥有最高的权重，
其次是 testcase，最后是testsuite。
即如果你已经在 api 层把一个常量赋值给了一个变量，
那么即使你在 testcase 或者 testsuite 层再去给这个变量赋值，
也不会生效。

我们可以看一下上面的脚本，我在 testcase 和 testsuite 中都有对 base_url 进行赋值，
但是最后测试报告中的 base_url 是 testcase 里的。

官方关于变量权重的说明，在
<a href="https://docs.httprunner.org/user/concepts/#variables-priority" target="_blank">这里
</a>

---
## 数据驱动
<br>

HttpRunner 中建议使用的数据文件是 CSV 



<br>