---
title: Golang的复合类型
date: 2016-08-15 21:22:46
tags: golang
---
Golang支持以下这些复合类型：
<!-- more -->
* 指针（pointer）
* 数组（array）
* 切片（slice）
* 字典（map）
* 通道（chan）
* 结构体（struct）
* 接口（interface）

## 数组（array）
数组就是指一系列同一类型数据的集合。
以下为一些常规的数组声明方法：

```golang
[32]byte					// 长度为32的数组，每个元素为一个字节
[2*N] struct { x, y int32 }			// 复杂类型数组
[1000]*float64					// 指针数组
[3][5]int 					// 二维数组
[2][2][2]float64 				// 等同于[2]([2]([2]float64))
```

在Golang中，数组长度在定义后就不可更改，和C语言不同的是Golang数组的长度是该数组类型的一个内置常量，可以用Golang的内置函数len()来获取：

```golang
arrLength := len(arr)
```
**元素访问**

```golang
for i := 0; i< len(array); i++ {
	fmt.Println("Element", i, "of arrary is", array[i])
}
```

Golang还提供了一个关键字range，用于便捷地遍历容器中的元素。当然，数组也是range的支持范围。

```golang
for i, v := range array{
	fmt.Println("Array element[", i, "]=", v)
}
```
在上面的例子里可以看到，range具有两个返回值，第一个返回值是元素的数组下标，第二个返回值是元素的值。

**值类型**
需要特别注意的是，在Golang中数组是一个值类型，所有的值类型变量在赋值和作为参数传递时都将产生一次复制动作。因此在函数体中无法修改传入的数组的内容，因为函数内操作的只是所传入数组的一个副本。

```golang
package main

import "fmt"

func modify(array [5]int) {
	array[0] = 10						// 试图修改数组的第一个元素
	fmt.Println("In modify(), array values:", array)
}

func main() {
	arrary := [5]int{1,2,3,4,5}				// 定义并初始化一个数组
	modify(array)						// 传递给一个函数，并试图在函数体内修改这个数组内容
	fmt.Println("In main(), arrary values:", array)
}
```

该程序的执行结果为：

```golang
In modify(), array values: [10 2 3 4 5]
In main(), array values: [1 2 3 4 5]
```

