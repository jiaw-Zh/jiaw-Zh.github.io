---
title: "Go 学习笔记"
date: 2022-04-20T10:02:07+08:00
categories: [Golang]
tags: ["2022",'Golang'] 
toc: true
---

[Golang 新手可能会踩的 50 个坑](https://segmentfault.com/a/1190000013739000)

## 分支结构--if

- 声明 if 语句的自用变量

    在每个 if 语句的后面，布尔表达式的前面，同时用分号隔开

```go
func main() {
    if a, c := f(), h(); a > 0 {
        println(a)
    } else if b := f(); b > 0 {
        println(a, b)
    } else {
        println(a, b, c)
    }
}
```

这里还要注意变量遮蔽的问题，即在 if 语句中声明的变量以及赋值行为，作用域仅在当前语句块中。

a