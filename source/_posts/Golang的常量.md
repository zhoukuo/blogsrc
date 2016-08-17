---
title: Golang的常量
date: 2016-08-14 17:58:23
tags: golang
---

在Golang中，常量可以是数字类型、布尔类型、字符串类型等。

## 字面常量
所谓字面常量，是指程序中硬编码的常量：

```golang
-12		// 整数类型的常量		
3.1415926	// 浮点类型的常量
3.2+12i		// 复数类型的常量
true		// 布尔类型的常量
"foo"		// 字符串常量
```
在其它语言中，常量通常有特定的类型，例如如果要指定一个值为－12的long类型常量，需要写成－12l，这有点违反人们的直观感受。golang的常量是无类型的，只要这个常量在相应类型的值域范围内，就可以作为该类型的常量。

## 常量定义
定义常量需要通过const关键字, Golang的常量可以限定类型，但不是必须的。如果没有指定类型，是无类型常量：

```golang
const Pi float64 = 3.1415926
const zero = 0.0		// 无类型浮点常量
const  {
	size int64 = 1024
	eof = -1		// 无类型整形常量
}
const u, v float32 = 0, 3	// u = 0.0, v = 3.0, 常量的多重赋值
const a, b, c = 3, 4, "foo"	// a = 3, b = 4, c = "foo", 无类型整形和字符串常量
```

在Golang中‘a’和"a"是不同的，‘a’是一个字符常量，而"a"是字符串常量：

```golang
const x = 'a'
fmt.Print(x) //输出97

const x = "a"
fmt.Print(x) //输出a
```

常量定义的右值可以是一个在编译期运行的表达式：

```golang
const mask = 1 << 3
```

不能出现运行期才能得出结果的表达式，比如下面的方式就会导致编译错误：

```golang
const Home = os.GetEnv("HOME")
```

## 预定义常量

Golang预定义了这几个常量：true、false、iota。

iota比较特殊，可以被认为是一个可被编译器修改的常量，在每一个const关键字出现时被重置为0，然后在下一个const出现之前，每出现一次iota，其所代表的数字会自动增1。

从以下的例子可以基本理解iota的用法：

```golang
const (			// iota被重置为0
	c0 = iota 	// c0 == 0
	c1 = iota 	// c1 == 1
	c2 = iota 	// c2 == 2
)
```

如果两个const的赋值语句的表达式是一样的，可以省略后一个赋值表达式：

```golang
const (
	c0 = iota 	// c0 == 0
	c1 		// c1 == 1
	c2 		// c2 == 2
)
```

## 枚举

枚举是一系列相关的常量，Golang不支持enum关键字，而是和普通的常量定义没什么区别。

```golang
const (
	Sunday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
	numberOfDays	// 这个常量没有导出
)
```

以大写字母开头的常量在包外可见。