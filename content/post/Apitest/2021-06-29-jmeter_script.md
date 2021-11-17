---
layout: post
title:  "Jmeter 可用的一些 BeanShell 脚本"
date:   2021-06-29 10:52:00 +0800
categories:  ['接口测试',"压力测试"]
tags: ['2021', Jmeter ]
toc: true
---

>一些 `Jmeter 中 BeanShell PreProcessor` 可用的脚本


判断文件中的某个参数是否传来空值，常用来判断时间戳和随机值

```
import java.util.Random;
import java.lang.String;

	public static String getReqID(String reqid){
		if("".equals(reqid)){
			return reqid;
			}
		else {
			return "${__RandomString(32,abcdefghijklmnopqrstuvwxyz0123456789)}";
            }
            }

    public static String getStamp(String stamp){
        if("".equals(stamp)){
            return stamp;
        }
        else {
            return "${__time(yyyyMMddHHmmss,)}";
        }
    }

String reqId = vars.get("reqId");
String stamp = vars.get("stamp");
String reqidutil = getReqID(reqId);
String stamputil = getStamp(stamp);
vars.put("RreqId", reqidutil);
vars.put("Rstamp", stamputil);
```

上面脚本中的参数 `reqId` 和 `stamp` 通过 `get` 方法从 `csv` 文件中获取，
如果 `csv` 文件中的值不为空，则返回脚本中函数生成的值；
如果 `csv` 文件中返回的值为空，则返回空。

最后返回的参数名是 `RreqId` 和 `Rstamp`,供其他地方调用。

