---
layout: post
title: Git core.autocrlf 设置问题
category: 工作中的坑
categories: Blog
description: 
---

### 背景
刚入职公司给配了个非常老旧的电脑，windows 系统，也不敢安装 wsl，怕卡住，就只能在 windows 上开发。

但是服务很多 shell 脚本，改完了想要扔到服务器上测一下，就发现脚本执行不了。

我也是第一次遇到这种情况，之前都是用 mac 的，或者有测试机。

出现这种情况是因为 git 的 core.autocrlf 设置的是 true。检出到 windows 上的脚本换行符就变成了 crlf，在 linux 下没法运行了。

虽说 git 推荐 windows 设置 autocrlf 为 true，但是在实际工作中不一定实用。

毕竟也有条件艰苦，开发机连不上 git，能连上 git 的本地机器又不一定能装双系统的情况……

### 解决方案
等新电脑的时候的临时方案是：
1. 对于换行符已经修改了的代码，需要做一下提交。因为 **autocrlf 改成 false，已经变成 crlf 的值也不会变回 lf**，最高效便捷的方法就是做一次提交，git 提交时候会把 crlf 换成 lf，不用自己手动处理了。
2. 脏了的代码库删掉。
3. git autocrlf 设置为 false，这样 checkout 到 windows 下也不会修改换行符。
4. 修改 vscode 的 eol 设置，改成 lf，防止后续对脚本的修改时，对换行符产生影响。
5. 重新拉下代码，这样就得到了一个干净的环境了。

长期方案：

拿到新电脑火速装了个 wsl，linux 就是好用……