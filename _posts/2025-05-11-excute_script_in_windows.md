---
layout: post
title: Golang 在 windows环境下执行脚本
category: 工作中的坑
categories: Blog
description: 
---

## Golang 在 windows 环境下执行脚本

### 背景
服务有一个功能，需要调用从外部传进来的脚本。

linux 环境下可以直接通过执行 `/bin/bash script.sh` 命令执行。当然，其实这个命令本身也是有问题的，bash 只有在执行命令的时候需要 -c 参数，脚本不用，但这个参数不影响使用，先不提。

windows 通过 `powershell -c script.ps1` 执行不了。

具体表现为，err 不为空，但是 err.Error 没有输出，脚本也不执行。

之前没接触过 window 平台，真是完全没有头绪啊……


### 排查过程+成因分析

#### 脚本不执行
尝试了一下在机器上打开 powershell，手动直接执行脚本，报 Execution_Policies 错误。

window 默认的脚本运行策略是 RemoteSigned
> 需要受信任的发布者对从 Internet 下载的脚本和配置文件（包括电子邮件和即时消息程序）的数字签名。

但是我们服务要执行的脚本都是自己编写的，所以可以直接将 ExcutionPolicy 设置成 ByPass，设置完成后可以直接运行。

#### 执行错误但是没有日志输出

排查这个问题的时候，中英文资料里都提到了这个 [stackover flow 的回答]。回答里提到：

1. 理想情况是，error code = 0，stdout 是执行输出，stderr 是错误输出。
2. 但是有些命令 stderr 为空，错误信息在 stdout。
3. 有些命令执行失败，error_code = 0, 但是 stderr 不为空。

总的来说，err 中不一定是用户想要的信息，要想获取一个命令执行的全部的信息，那就需要 err + stderr + stdout，然后根据实际情况做调整。

按照这个回答，通过 CombinedOutput 先把 output 打出来，果然能拿到执行出错的原因。

从这个结果反推，再去看 CombinedOutput 代码。

```go
func (c *Cmd) CombinedOutput() ([]byte, error) {
	if c.Stdout != nil {
		return nil, errors.New("exec: Stdout already set")
	}
	if c.Stderr != nil {
		return nil, errors.New("exec: Stderr already set")
	}
	var b bytes.Buffer
	c.Stdout = &b
	c.Stderr = &b
	err := c.Run()
	return b.Bytes(), err
}
```

CombinedOutput 将 stdout 和 stderr 绑定到 output，cmd.Run() 的返回绑定到 err。

再进一步查看源码可知，Run = Start + Wait，而 Start 和 Wait 的错误，主要是尝试执行 cmd 时候碰到的错误，比如命令不存在，Wait 时候命令没有 Start 等等，而返回的 err 也是 exec 包自己 errors.New() 出来的内容，不能作为命令执行的结果参考。

所以在使用 cmd 执行命令时候，最好把 err 和 output 都获取到，err 不成功时，都打印出来看看，一开始就写成这种格式，防止出现出错之后没办法排查，需要再加日志上线的尴尬情况。

### 解决

脚本不执行的问题其实非常好解决，而且所有调用脚本的操作都可以用以下模板

``` go
    cmd.Command("powershell", "-ExecutionPolicy", "Passby", "-File", $file_name)
```

对于所有需要查看返回值的命令，都可以将 Run 操作替换成 CombinedOutput 实现
```go
    output, err := cmd.CombinedOutput() 
    if err != nil {
        fmt.Printf("fail to exec cmd:%s, err:%w, output:%s", cmd.String(), err, string(output))
    }
```

Windows 真是一步一个坑啊，每一步都像走在刀尖上……

[stackover flow 的回答]:https://stackoverflow.com/questions/18159704/how-to-debug-exit-status-1-error-when-running-exec-command-in-golang
