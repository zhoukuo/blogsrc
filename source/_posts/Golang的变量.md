---
title: Golang的变量
date: 2016-08-14 16:21:56
tags: golang
---
Golang变量与C接近，但具备更大的灵活性。

## 变量声明
Golang的变量声明方式与C/C++语言有明显的不同

```golang
var v1 int
var v2 string
var v3 [10]int
var v4 []int
var v5 struct {
	f int
}
var v6 *int
var v7 map[string]int
var v8 func(a int) int
```

* Golang引入了关键字var
* 类型信息放在变量名之后
* 变量声明语句不需要使用分号(;)作为结束符

多个变量可以放在一个var后声明，当需要放在小括号里(注意，不是大括号)：

```golang
var (
	v1 int
	v2 string
)
```

## 变量初始化
变量初始化时，var不再是必要的元素

```golang
var v1 int = 10 // 正确的使用方式1
var v2 = 10     // 正确的使用方式2，编译器可以自动推导出v2的类型
v3 := 10        // 正确的使用方式3，编译器可以自动推导出v3的类型，仅用于局部变量
```
与C语言初始化变量不同
* 指定类型已不再是必须的，编译器可以从右值自动推导
* := 用于明确表达同时进行变量声明和初始化，所以不能对声明过的变量使用

## 变量赋值
golang变量赋值和其它语言一致，但golang提供了C/C++程序员期盼多年的多重赋值功能。

```golang
i, j = j, i
```

## 匿名变量

因为golang支持多返回值，因此在调用函数时为了获取一个值，却不得不定义一堆没用的变量。这种情况可以通过使用匿名变量

```golang
func GetName() (firstName, lastName, nickName string) {
	return "May", "Chan", "Chibi Maruko"
}
```

若只想获得nikeName，则可以这样

```golang
_, _, nickName := GetName()
```

这种用法可以让代码非常清晰，基本屏蔽了混淆阅读视线的内容。
