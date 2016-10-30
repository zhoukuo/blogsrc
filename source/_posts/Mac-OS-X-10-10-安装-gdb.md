---
title: 转：Mac OS X 10.10 安装 gdb
date: 2016-10-15 09:11:27
tags: golang
---
LiteIDE调试Go语言需要安装GDB
<!--more-->
## 1.先解决brew不能使用的问题
```bash
cd /usr/local/Library
Git pull origin master

brew update
brew prune
brew doctor
```

参考:http://vancelucas.com/blog/fixing-homebrew-on-osx-yosemite-10-10/

## 2.用Homebrew 安装 gdb

(1)
```bash
# brew tap homebrew/dupes
```
(2)
```bash
# brew install gdb
```
(3)有可能提示:
```bash
Warning: No developer tools installed.
You should install the Command Line Tools.
Run `xcode-select --install` to install them.
那就先安装the Command Line Tools.
# xcode-select --install
```

## 3.给gdb制作证书并授权：http://plotcup.com/a/129

如果没有证书，会出现如下提示：
```bash
(gdb) run
Starting program: /Users/wenke/go/src/gdb/main
Unable to find Mach task port for process-id 18420: (os/kern) failure (0x5).
 (please check gdb is codesigned - see taskgated(8))
```
