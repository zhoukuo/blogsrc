---
title: 转：关于 COM 的无责任评论
date: 2016-07-25 16:52:02
tags: c
---
为什么有 COM ？我的理解是，MS 要给对象的二进制表达规范一种标准，好做到二进制模块的复用。<!-- more -->到 COM 的诞生日为止，C++ 是最适合实现模块对象化的语言，直到现在 C++ 依旧是可以用面向对象的方式直接生成的本地码的最佳工具。可惜 C++ 的实现方案并没有标准，比如对象中数据的布局规则，函数调用的参数传递方式，返回值的处理方式，多继承的实现，等等。

MS 在设计 COM 时采取最小原则，需要最少的统一的 C++ 实现方式，那就是虚表的结构。要求虚表必须放在对象物理地址的头四个字节，其它的都不要求了。所以我们看到的是，

1. COM 必须要求界面是一个纯虚类，也就是说不准把对象内部的变量暴露出来，不准有非虚的函数，这样，所有的函数入口地址信息都可以从虚表取到。
2. 接口不准多继承，这样保证了接口虚表的唯一性。

但是，对象的实现可以是多继承多个接口的。为了解决这个问题，COM 在 IUNKNOWN 里要求每个对象都必须实现 QueryInterface 。这个函数其实就是为了解决不同的 C++ 编译器对多继承的实现不一致的情况设定的。

比如有:
```c
class A; 
class B;
class C : public A, public B
A* a = &ObjectC;
B* b = &ObjectC;
```
这两条代码的实现很可能在不同的编译器上不一样， a 和 b 的值一般情况下也不等同。

同一个对象 C ，用 C 指针, B 指针，和 A 指针指向它的时候物理地址可以是不一样的。只有提供一个转换方法，也就是 COM 中要求的 QueryInterface，才可以统一正确的解决这个问题。

COM 要求 QueryInterface 的实现，就是给 COM 的对象提供了一种 C++ 中 dynamic_cast 的能力。正因如此，COM 要求 QueryInterface 有和dynamic_cast 同样的特性。即从 A QueryInterface 到 B 能够成功的话，B也可以 QueryInterface 回 A 。只要是同一个对象，对象中任意两个接口间都要求可以自由的转换。

现在我们来看为什么 IUnknown 中必须实现 AddRef 和 Release 。我们知道，C++ 的对象是不要求一定要保留一个自己的引用计数的。COM 为什么把这个作为一个必须的要求？

我认为，还是因为 COM 可以支持对象同时提供多个不相关接口所致。假设一个对象同时提供了一个阿猫接口，又提供了一个阿狗接口。这个对象可以看成是阿猫阿狗的聚合体，阿猫阿狗是同一层次没有从属关系的。当我们把这个对象分别交给两个东西去操作的时候，一旦使用完毕，我们无法决定是阿猫的部分决定释放自己，还是阿狗来释放，这是无法从程序逻辑上控制的。所以必须给对象加一个接口引用次数的机制。

以上，IUnknown 的三个必须实现的方法，即每个 COM 对象都必要的方法。的确是用来模拟 C++ 对象最精练的方式。但我认为，在实际运用中，这并不是最合理的结构。首先我们并不需要对象要严格的遵循 C++ 对象的实现。比如阿猫和阿狗的聚合体，一旦分离了阿猫和阿狗的接口，那么从阿猫中得到阿狗或者从阿狗中得到阿猫都是没有意义的。

其次，许多对象都是对单一接口的实现，而无须多继承多个接口。这种情况下，QueryInterface 几乎不被使用。转之，AddRef 和 Release 也非必要的需求。对接口的引用计数这里实际是对对象的引用次数的计数，而对对象的引用计数实际上是可以由引用者来计的，可以把引用量记在对象之外而不是之内。我们可以把工程中碰到的对象分成两类，一类是单件，永远都只有一份实例，外部保留住引用次数就可以保证最后安全的被销毁。而另一类，是从 Factory可以重复生产出多份的对象。后一种对象有开发人员显式的构造和销毁的过程，我们看这一过程，如果按 COM 的实现，构造的过程是由 Factory 完成的，而销毁的过程是由对象自己实现(调用 Release)。这不奇怪吗？按照我们普遍的设计原则，构造和销毁应该在同一层次上，由同一个东西来操作的。我现在在设计新的游戏引擎的底层架构，原本模仿 COM 来做的。最近越来越觉得有些问题，不吐不快。已经下决心重新设计一套更合适的方案了。
<完>

原文地址：http://blog.codingnow.com/2005/09/about_com.html