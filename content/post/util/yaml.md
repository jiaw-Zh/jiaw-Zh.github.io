---
title: "Yaml 文件格式的介绍"
date: 2023-03-08T17:19:18+08:00
draft: false
categories: ['工具']
tags:  ['2023']
---

yaml 也是一种标记语言，可以简单表达字典、列表和其他基本数据类型的形态。


yaml 语法规则如下：
1. 大小写敏感。
2. 使用缩进表示层级关系。
3. 使用空格键缩进，而非Tab键缩进
4. 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可。
5. 文件中的字符串不需要使用引号标注，但若字符串包含有特殊字符则需用引号标注；
6. 注释标识为#

## Yaml 字典介绍
字典 {"name": "test_yaml", "result": "success"} 写成Yaml的格式如下：
```yaml
name: test_yaml
result: success
```


## Yaml 列表介绍
列表 ["a", "b", "c"] 写成Yaml的格式如下：
```yaml
- a
- b
- c
```


## 字典和列表基本操作
### 字典嵌套字典

```json
{
    "person1": {
        "name": "xiaoming",
        "age": 18
},
    "person2": {
        "name": "xiaohong",
        "age": 20
}
}
```
写成Yaml的格式如下：
```yaml
person1:
    name: xiaoming
    age: 18
person2:
    name: xiaohong
    age: 20
```

### 字典嵌套列表

{person: ["1", "2", "3"]} 写成Yaml的格式如下：
```yaml
person:
    - 1
    - 2
    - 3
```

### 列表嵌套字典
```json
[{
	"username1": "test1"
}, {
	"password1": "111",
	"username2": "test2",
	"password2": "222"
}]
```
写成Yaml的格式如下：
```yaml
- username1: "test1"
- password1: "111"
  username2: "test2"
  password2: "222"
```

### 列表嵌套列表
[["a","b","c"],["1","2","3"]] 写成Yaml的格式如下：

```yaml
-
  - "a"
  - "b"
  - "c"
-
  - "1"
  - "2"
  - "3"
```