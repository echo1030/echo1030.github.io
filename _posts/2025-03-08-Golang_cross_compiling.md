---
layout: post
title: Golang 跨平台兼容性相关问题
categories: Blog
description: 面试 + 实际工作对 golang 跨平台特性的心得体会
---
### Golang 跨平台特性

过两天有个面试，JD 上写着要了解 Golang 跨平台编译，linux 和 windows 兼容，这个之前还真没用过，所以了解一下。

我印象 Golang的一个优势就是他的跨平台特性做的比较好。主要是因为以下几方面做的比较好。

1. 编译器设计。交叉编译方便，支持不同 os，不同 arch。
2. 标准库设计。操作 I/O，网络，并发等都是用的是标准库统一接口，方便跨平台使用。
3. 默认适用静态链接，尽可能少的使用动态链接。所有的库都被放到可执行文件中，降低平台影响。

### 交叉编译参数

主要参数有 3 个

| 参数名称   | 参数含义                          | 可选内容                                                     |
| ---------- | --------------------------------- | ------------------------------------------------------------ |
| CGO_ENABLE | 代表是否可以使用库里用 C 写的函数 | 0/1                                                          |
| GOOS       | 目标平台的操作系统                | 主流的有 darwin(Mac OS)，linux，windows，freebds（类似unix）<br>小众的有：netbds，openbds，plan9 |
| GOARCH     | 目标平台的体系架构                | 主流 arm，amd64，386等                                       |

编译时候分两种情况

1. 无需使用 cgo

   此时只要指定 GOOS 和 GOARCH 即可。如`CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build`

2. 需要使用 cgo

   mac 和 linux 需要安装 gcc，添加环境变量。

   window 安装 MinGW，并把 MinGW 的 bin 目录添加到系统的 PATH 环境变量中。

### 条件编译

golang 在不同 OS/ARCH 相同接口可能有不同实现，例如在 window 平台有的参数，linux 平台没有，此时如果代码中通过

```
if os == window
else 
```
形式实现，则无法编译通过，所以需要使用条件编译。

条件编译有两种形式

1. 通过 `build tag` 实现

   在文件的最开始位置，加上一行 `//go:build $tag`，留一行空行。这样只有执行 `go build -tags="tag"` 的时候，才能被编译。

   **当 tag = $GOOS/\$GOOARCH 时，可以不带 -tags参数直接build**

2. 通过后缀实现

   通过以下后缀，可以根据系统和架构决定是否跳过该文件。提高效率，无需读取文件。

   _${GOOS}.go

   _${GOARCH}.go

   _${GOOS\}\_\${GOARCH}.go

   ### 条件编译实操

   针对这个后缀，在工作中发现了一些使用心得。

   最开始不知道怎么用，但是源码是最好的老师。看了一些 go 的 exec，os 之类的源码，大致了解了使用方法。

   1. 针对 class A，a.go 定义针对外部的接口，接口中调用对应的函数完成具体实现。
   2. 分别定义 a_$os.go 完成不同平台的实现。
   3. 编译时只会加载指定 GOOS 的文件进行编译。
   4. GOARCH 同理

   示例如下

   ```
   // a.go
   func (a *A) F {
      a.f()
   }
   
   // a_linux.go 
   func (a *A) f {
      // do with linux feature
   }

   // a_windows.go 
   func (a *A) f {
      // do with windows feature
   }
   ```

### 不同 OS 表现不同的包

1. filepath 用于处理文件路径的不同表达形式
2. os 用于执行系统命令
3. bufio 库用于读取文件，并处理换行符等
4. ...

### 后续
后续工作中接触到新的部分再补充