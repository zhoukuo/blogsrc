---
title: Rob Pike：走进Go语言
date: 2016-08-11 22:32:48
tags: golang
categories: golang
---
> 本文整理自Google首席工程师Rob Pike的演讲，主要讲述的了Go的部分设计原理和初衷，也有提到Go语言在Google内外的应用现状。但本文的目的更多的是关于软件工程而不是编程语言的设计，更准确地说是如何设计编程语言来帮助软件工程。
<!-- more -->

![](http://static.oschina.net/uploads/img/201410/15150257_6HPb.jpg)

## 序言：关于Go

Go语言是一个开源、并发、高效、简单、有趣（但对某些人来说可能很无聊）的编程语言，支持垃圾回收（GC），具有很好的可伸缩性。Go是从2007年末由Robert Griesemer, Rob Pike, Ken Thompson主持开发，后来还加入了Ian Lance Taylor, Russ Cox等人，并最终于2009年11月开源，在2012年早些时候发布了Go 1稳定版本。现在Go的开发已经是完全开放的，并且拥有一个活跃的社区。

Go最初是为了解决Google的问题而开发的，要知道Google面临着很多大问题。Google的中服务器最主要是C++编写的，除此之外还有很多Java、Python代码。另外，Google还有数千名工程师、无数行代码、庞大的分布式构建系统以及数不清的机器（我们认为相对于一个中等规模的集群）。Google的开发可能很慢，甚至笨拙，但它总是很有效。

所以毫无疑问Go对“大硬件”的支持非常好，也适合“大软件”的开发。CSDN之前也编译了一批Rob Pike的文章——[Go语言之父谈Go：大道至简](http://www.csdn.net/article/2012-07-05/2807113-less-is-exponentially-more)，在这里Rob描述了Go的创作起源和初衷。

![](http://static.oschina.net/uploads/img/201410/15150257_Gt68.jpg)

## 为什么应该用Go？

Go是为了帮助人们阅读、调试和维护大型软件系统而生的，所以目标是：

* 不再缓慢
* 不再笨拙
* 提高效率
* 保持（甚至提升）扩展性

但是在使用C++或者Java开发中却常常遇到各种问题：

* 构建缓慢
* 依赖性难以控制
* 每个编程语言都使用不同的语言子集
* 程序难以理解（文档等原因）
* 重复工作
* 更新成本高
* 版本交叉
* 自动化不方便（工具问题）
* 跨语言构建

而Go语言则是为了解决这些问题而设计的。

另外，C语言的依赖一直是个大问题，包括依赖叠加、编译时引入依赖的情况都很难处理，同时你也没办法查清哪些依赖是可以删除的，那些不可以。在C++中，这一点变得更加明显：

* 每个类里都有#include文件
* #include文件中有代码（而不仅仅是声明）
* #ifndef的残留

所以一直无法在一台机器上构建大型Google二进制。（To build a large Google binary on a single computer is impractical.）

当然，工具确实很有帮助，于是做了如下改进：

* 新的分布式构建系统
* 不再需要Makefile（但仍然使用BUILD文件）
* 多缓存
* 多复杂度（大程序本身所具有的）

即使在Google的分布式构建系统的的帮助下，大型构建工程依然会花费不少时间（以其中一个二进制文件为例，在2007年花了45分钟，现在是27分钟）。生活质量还是太低。

## 走进Go语言

我们都希望拥有更高质量的生活，所以必须解决这些问题，所以就有了最初的想法：

* 必须是可扩展的
* 适合大型程序、大型团队以及拥有大量依赖的应用
* 必须易于接近，例如接近C语言那样。
* 现代化
* 适合多核机器
* 适合网络机器
* 适合Web开发

Go语言的设计是以软件工程为目标，所以它有这些优点：

* 清晰的依赖
* 清晰的语法
* 清晰的语义学
* 简单的模型（垃圾回收和并发性）
* 便捷的工具（go tool、gofmt、godoc、gofix等）

还有那些问题？

Go语言目前所面临的最大问题在于，还没有足够的经验来证明Go是否真的是一个成功的产品，缺少大型应用实践。但是在Google内部，如golang.org、youtube.com、dl.google.com都已经开始使用Go语言开发，除此之外还有一些其它小应用（有的是在GAE上）也选择使用Go；而在Google之外，BBC Worldwide、Canonical、Heroku、Nokia、SoundCloud也都在尝试Go。

总而言之，Go是由软件工程驱动的编程语言，但富有成效并且有趣，这样的设计非常高产。

Rob Pike演讲的Slide可以在[这里](http://talks.golang.org/2012/splash.slide)看到。
<完>

原文地址：http://www.csdn.net/article/2012-11-01/2811380-Go-in-Google