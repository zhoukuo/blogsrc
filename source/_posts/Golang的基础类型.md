---
title: Golang的基础类型
date: 2016-08-14 19:17:37
tags: golang
---
Golang内置了以下这些基础类型：
<!-- more -->

* 布尔类型：bool
* 整型：int8、byte、int16、int、uint、uintptr等
* 浮点类型：float32、float64
* 复数类型：complex64、complex128
* 字符串：string
* 字符类型：byte、rune
* 错误类型：error

## 布尔类型
Golang的布尔类型和其它语言基本一致：

```golang
var v1 bool
v1 = true
v2 := (1 == 2)  // v2也会被推导为bool类型
```

布尔类型不能接受其它类型的赋值，不支持自动或强制的类型转换。以下是错误的：

```golang
var b bool
b = 1       // 编译错误
b = bool(1) // 编译错误
```
以下的用法是正确的：

```golang
var b bool
b = (1!=0)
fmt.Println("Result:", b)   // 打印结果为Result: true
```

## 整型

Golang支持以下所示的这些整型类型：
* int8
* uint8(即byte)
* int16
* uint16
* int32
* uint32
* int64
* uint64
* int
* uint
* uintptr

需要注意的是，int和int32在Golang里被认为是两种不同的类型，编译器也不会帮你自动做类型转换，比如以下的例子会有编译错误:

```golang
var value2 int32
varlue1 := 64       // value1将会被自动推导为int类型
varlue2 = value1    // 编译错误
```

使用强制类型转换可以解决这个编译错误：

```golang
value2 = int32(value1)
```
在做强制类型转换时，需要注意长度被截断和值溢出问题。

数值运算和比较运算与C语言完全一致。但位运算中取反在C语言中是~x，而在Golang中是^x。

## 浮点类型

Golang中的浮点类型采用IEEE-754标准的表达方式，定义了两个类型：float32和float64。其中float32等价于C语言的float类型，float64等价于C语言中的double类型。

```golang
var fvalue1 float32

fvalue1 = 12
fvalue2 = 12.0      // 如果不加小数点，fvalue2会被推导为整型而不是浮点型
```
需要注意的是，fvalue2的类型将被推导为float64，而不管赋给它的素质是否是用32位长度表示的。

因为浮点数不是一种精确的表达方式，所以不能用＝＝来判断两个浮点数是否相等。

## 复数类型
<略>

## 字符串

在Golang中，字符串也是一种基本类型：

```golang
var str string          // 声明一个字符串变量
str = "Hello wolrd"     // 字符串赋值
ch := str[0]            // 取字符串的第一个字符
fmt.Println("The length of \"%s\" is %d.\n", str, len(str))
fmt.Println("The first character of \"%s\" is %c.\n, str, ch)
```

需要注意的是，字符串的内容不能在初始化后被修改。

平常常用的字符串操作包括：
* 字符串连接：x + y
* 字符串长度：len(s)
* 取字符：          s[i]

更多字符串操作，请参考标准库strings包。

Golang支持两种方式的字符串遍历，一种是以字节数组的方式遍历：

```golang
str := "Hello, 世界"
n := len(str)
for i := 0; i < n; i++ {
    ch := str[i]        //依据下标取字符串中的字符，类型为byte
    fmt.Println(i, ch)
}
```
可以看出，这个字符串长度为13。尽管从直观上来说，这个字符串应该只有9个字符，这是因为每个中文字符在UTF8中占3个字节，而不是1个字节。

另一种是以Unicode字符遍历：

```golang
str := "Hello, 世界"
for i, ch := range str {
    fmt.Println(i, ch)  //ch的类型为rune
}
```
以Unicode字符方式遍历时，每个字符的类型是rune，而不是byte。

## 字符类型

在Golang中支持两个字符类型，一个是byte(实际上是uint8的别名)，代表UTF－8字符串的单个字节的值；另一个是rune，代表单个Unicode字符。

出于简化语言的考虑，Golang的多数API都假设字符串为UTF－8编码。尽管Unicode字符在标准库中有支持，但实际上较少使用。
