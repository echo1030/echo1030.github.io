---
layout: post
title: github page + jeklly 搭建过程及遇到的问题
categories: Blog
description: question and solution when use github page and jeklly to build a blog 
---

搜索自建技术blog，大家首先推荐的就是github page + jeklly。参考网上的教程，记录一下我遇到的问题

## 搭博客的两种方式

* 直接fork一个教程里的分支，修改仓库名称为自己的用户名称。修改各种设置，删掉原博的文章，毕竟要有版权意识嘛

* 从jeklly的theme/github 上选择自己喜欢的风格，download之后，在本地修改。然后推送到github page上

我选择了第二种，因为第一种可能会出现配置删除不全，导致推送时候总是向原博上推送的乌龙，而且直接fork，减少了修改的流程，可能导致对jeklly了解不够充分。

## 修改内容

我参考的是这个大佬的博客，风格简约，配色也好看。down下来之后，除了修改了各种配置，还做了符合我现状的修改。

https://github.com/mzlogin/mzlogin.github.io

1. 删掉link。不像大佬有好多的朋友，有互推。link对于当前的我，暂时没啥用，删掉。

2. 去掉评论模块。评论模块需要申请新的用户和token，这个部分还没看，可以暂时不要。
3. sidebar，大佬们的github都是各种Repositories，我的都是空的，所以暂时换成了博客分类，希望能早一天换成自己的Repositories吧

4. about部分。在其他的平台没有啥专栏，所以about部分空空的，没啥可写，所以修改成了学习和工作经历。

## 修改具体实现

1. 最开始找不到类似about.html和link.html这类文件在哪里，想不明白为什么只有一个index.html也能工作。后来又重新翻了下jeklly的文档(http://jekyllcn.com/docs/pages/)。文档中提到，将```HTML或者Markdown```文件放在站点根目录下，或者在根目录下创建一个文件夹用来存放，都能实现创建页面的功能，只是url的格式不同。然后我就在clone的文件下找到了page目录，里边存放了包括about，link等md文件。如果不需要哪个页面，删掉或者修改后缀就可以。同时注意修改_config.yml文件中相关的配置，会涉及到heaer的显示。

2. 由于我先在还没申请评disqus的账户，所以暂时需要把评论功能去掉。当前代码是在 \_layout.html的各个模板中，引用了\_include/comment.html，完成评论模块的添加。暂时注释掉即可，申请disqus账号后，取消掉所有注释，模块即可生效。

3. 修改和2类似，在\_layout/*.html中，引用了repositories的html文件的代码删掉，换成分类的html

4. 之前下载过另外一个模板的代码，左边是学历工作经验，右侧是skill和英语水平。学历和工作部分还有timeline链接。

   直接把代码照办过来却不生效。点击的时候报错缺少文件。按照提示把缺少的文件补全，还是没有效果。

   我对html的了解几乎为0，猜测是代码和模板本身的风格和主题有某种冲突，且about的优先级比较低，造成代码不生效。

   暂时解决方案为左侧用markdown书写，右侧写成一个html文件，用类似添加sidebar的方法，嵌套在layout/about.html中，算是暂时完成了这个功能。后续待对html了解更加深入后，再改成想要的风格。毕竟，要先把博客搭起来啊

## 致谢

博客的模板和fork的方法参见https://github.com/mzlogin/mzlogin.github.io，感谢大佬！



