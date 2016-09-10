---
title: shell的使用(2)
date: 2016-09-10 14:23:34
tags: unix
---
shell是对用户提出的运行程序的请求进行解释的程序。对大多数UNIX用户而言，shell是系统中最重要的程序。除了使用文本编辑器以外，用户大部分时间都在shell中工作。一旦学会使用shell，就可以不必求助于传统语言编程，而解决很多复杂的问题。
<!--more-->

## 命令行结构

最简单的命令就是一个字(word)，通常是一个可执行的文件名。

```bash
mbp:blog zhoukuo$ who
zhoukuo  console  Aug 23 21:25 
zhoukuo  ttys000  Aug 23 21:25 
```

一条命令通常以换行结束，但有时分号；也可用作命令结束符：

```bash
mbp:blog zhoukuo$ date; who
2016年 8月23日 星期二 22时34分26秒 CST
zhoukuo  console  Aug 23 21:25 
zhoukuo  ttys000  Aug 23 21:25 
```
可以将date;who的输出送到一个管道：

```bash
mbp:blog zhoukuo$ (date;who)|wc
       3      15     112
```
date和who的输出被串连成单个数据流，送入一个管道。