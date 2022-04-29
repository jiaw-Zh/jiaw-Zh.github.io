---
title: "Go 学习笔记"
date: 2022-04-20T10:02:07+08:00
categories: [Golang]
tags: ["2022",'Golang'] 
toc: true
draft: true
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

## 循环结构--for（只有一种）

### 经典 for 循环语句

```go
var sum int
for i := 0; i < 10; i++ {
    sum += i
}
println(sum)
```

### for range 循环形式

for 前置语句; 条件语句; 后置语句 { 循环体 }

```go
var sl = []int{1, 2, 3, 4, 5, 6}
for s, v := range sl {
    fmt.Printf("sl[%d] = %d\n", s, v)
}
```

使用 for range 遍历字符串

```go
var s = "中国人"
for i, v := range s {
    fmt.Printf("%d %s 0x%x\n", i, string(v), v)
}
```
输出结果：
```
0 中 0x4e2d
3 国 0x56fd
6 人 0x4eba
```
for range 对于 string 类型来说，每次循环得到的 v 值是一个 Unicode 字符码点，也就是 rune 类型值，而不是一个字节，返回的第一个值 i 为该 Unicode 字符码点的内存编码（UTF-8）的第一个字节在字符串内存序列中的位置。