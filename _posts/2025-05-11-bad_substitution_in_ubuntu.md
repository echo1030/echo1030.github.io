---
layout: post
title: Unbuntu Bad Substitution
category: 工作中的坑
categories: Blog
description: 
---

### 背景

windows 电脑的 wsl 默认安装了 ubuntu 系统，在脚本开发时候，服务器上可以正常运行的脚本在本地测试时，字符串拼接语句，碰到 bad substitution 错误。

### 原因分析以及解决
出现这个问题的原因，是我在 ubuntu 系统上，通过 `sh test.sh` 执行测试脚本。而 sh 在不同的系统里，指向的解释器不一致，在我常接触的三种系统中

|系统|sh 实际指向|
|---|---|
|mac|sh 本身|
|centos|sh-> bash|
|ubuntu|sh-> dash|

linux 系统中，sh 通常不指向 sh 本身，而是指向其他解释器。

为解决 bad substitution 的问题，直接使用 `/bin/bash test.sh` 或者给 test.sh 加可执行权限，并且`./test.sh`就可以了（前提是 tesh.sh 使用了 bash 解释器）。

### 由问题引申出来的一些小知识

#### shell 是什么
UNIX环境高级编程里定义
> shell 是一个特殊的应用程序，为运行其他应用程序提供了一个接口。

> shell 是一个命令行解释器，它读取用户输入，然后执行命令。

> shell 的用户输入可以来自于终端和脚本。

简单理解，就是 shell 作为用户和内核的桥梁，将用户命令转换为系统调用，已达到用户的需求。

#### shell 有哪些
由于 （类）unix 系统是开源的，所以 shell 也有很多的版本。一些 shell 相关操作如下

``` shell
# 查看当前系统支持哪些版本的 shell
ls /etc/shell

# 切换到指定 shell，如
/bin/bash

# 查看当前使用的是哪个版本的 shell
echo $SHELL
```

#### shell 和脚本之间的关系

当执行 `$shell script.sh` 的时候，使用的 shell 命令的优先级，高于 script.sh 里指定的解释器的优先级。

这也是为什么 ubuntu sh 默认指向 dash 会导致 bad substitution。

所以需要注意
1. 要不就直接给脚本加权限，然后 `./script.sh` 执行脚本。
2. 要不就要确认使用的 shell 命令和想要执行的命令一致。

#### Golang 里调用 shell 命令或脚本

1. 执行命令，要加 -c 参数，因为 shell 本身是和脚本搭配使用，如果单独的命令，要用 -c 指定。`exec.Command("/bin/bash", "-c", "$commond")`
2. 执行脚本。`exec.Command("/bin/bash", "script.sh")`

后续想到什么再补充吧。