---
layout: post
title: GitHub Page + Jekyll 搭建过程及遇到的问题
categories: Blog
description: question and solution when use github page and jeklly to build a blog 
---

### GitHub Pages 原理

GitHub Pages 文档：<https://docs.github.com/zh/pages>，讲的很明白。以下记录一些使用中的摘要。

#### About GitHub Pages  

GitHub Pages 是一项静态站点托管服务，它直接从 上的仓库获取 HTML、CSS 和 JavaScript 文件，（可选）通过构建过程运行文件，然后发布网站。

#### 站点类型 

分为项目，个人和组织。针对个人，需要创建用户名同名repository，GitHub Pages  会根据用户选择的构建方式，发布网站。访问地址为 `http://${username}.GitHub.io`

#### 发布方式

可以选择 GitHub Action。也可选择从分支发布，如果不需要对发布流程做过多控制，则可制定分支和目录。GitHub Pages 检测到该目录下有推送时，就会进行发布。

#### 静态站点生成器

`如果从源分支发布站点，GitHub Pages 将默认使用 Jekyll 生成站点`。这也是为什么大家一般建议 GitHub Pages + Jekyll 组合进行网站搭建，因为原生支持，可以简单些。

### Jekyll 原理

Jekyll 文档详见<https://jekyllrb.com/docs/>。

Jekyll 功能：`Transform your plain text into static websites and blogs.` 即将文本转为静态网站。

其优点在于，把网站模板和内容进行分离。不需要在每个博文上进行模板内容的创建，只需要关注博文本身即可。

对于 Jekyll，我认为主要理解各个目录的功能，直到修改 conf，修改排版，发布博文去需要操作哪些文件即可。再具体的 html 相关知识，可以用到了再看。

以下是一个目录示例（不完全 match 我当前目录，仅做参考）

```
.
├── _config.yml
├── _data
│   └── members.yml
├── _drafts
│   ├── begin-with-the-crazy-ideas.md
│   └── on-simplicity-in-technology.md
├── _includes
│   ├── footer.html
│   └── header.html
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
│   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _pages
│   ├── _about.md
│   └── _wiki.md
├── _site
├── .Jekyll-cache
│   └── Jekyll
│       └── Cache
│           └── [...]
├── .Jekyll-metadata
└── index.html # can also be an 'index.md' with valid front matter
```

| **File / Directory**       | **Description**                                              |
| -------------------------- | ------------------------------------------------------------ |
| _config.yml                | 全局配置文件，包括 url，author，navigation 等，可以通过 site.${var_name} 访问 |
| _index.html                | 主页，网站入口                                               |
| _draft                     | 草稿，可选                                                   |
| _data                      | blog 中可以复用的数据。通过 `site.data.${filename}.${varname}` 访问 |
| _include                   | blog 中可以复用的 layout，比如 footer，header 等             |
| _layout                    | 不同类型页面的排版。通过layout变量制定适用那种排版           |
| _posts                     | 博文内容，按照 `yyyy-mm-dd-${title_name}` 格式命名           |
| _pages                     | 其他需要生成的网页。可以用 md 格式。Jekyll 根据 md 在 site 下生成一个同名目录，目录下存储 index.html |
| _site                      | 生成的网页，需要加入 gitignore                               |
| _assets                    | 一些模板需要的文件，基本不需要修改                           |
| 一些 Jekyll 需要文件(隐藏) | 加入 gitignore                                               |

根据以上信息

1. 修改 conf 比如博客 title 等信息，在_config.yml
2. 修改布局关注 layout 和 include
3. 发布 blog 关注 posts
4. 增加页面关注 pages
5. 修改 icon 等在 assets 下
6. 修改主页在 index.html

### 搭建过程

网上各种教程很全面，不过多赘述了，基本就是

1. GitHub 创建仓库，设置
2. Fork 对应模板+修改配置信息+定制化
3. 写博文
4. 推送

下边是我自己搭建过程中的一些问题

1. 最开始找不到类似 about.html 和 link.html 这类文件在哪里，想不明白为什么只有一个 index.html 也能工作。后来又重新翻了下 jeklly 的文档<http://jekyllcn.com/docs/pages/>。文档中提到，将 `HTML或者Markdown` 文件放在站点根目录下，或者在根目录下创建一个文件夹用来存放，都能实现创建页面的功能，只是 url 的格式不同。然后我就在 clone 的文件下找到了 page 目录，里边存放了包括 about，link 等 md 文件。如果不需要哪个页面，删掉或者修改后缀就可以。同时注意修改 _config.yml 文件中相关的配置，会涉及到 heaer 的显示。

2. 由于我先在还没申请评 disqus 的账户，所以暂时需要把评论功能去掉。当前代码是在  \_layout.html的各个模板中，引用了 \_include/comment.html，完成评论模块的添加。暂时注释掉即可，申请 disqus 账号后，取消掉所有注释，模块即可生效。

3. 修改和2类似，在\_layout/*.html 中，引用了 repositories 的 html 文件的代码删掉，换成分类的 html

4. 之前下载过另外一个模板的代码，左边是学历工作经验，右侧是skill和英语水平。学历和工作部分还有 timeline 链接。

   直接把代码照办过来却不生效。点击的时候报错缺少文件。按照提示把缺少的文件补全，还是没有效果。

   我对 html 的了解几乎为0，猜测是代码和模板本身的风格和主题有某种冲突，且 about 的优先级比较低，造成代码不生效。

   暂时解决方案为左侧用 markdown 书写，右侧写成一个 html 文件，用类似添加 sidebar 的方法，嵌套在 layout/about.html中，算是暂时完成了这个功能。后续待对 html 了解更加深入后，再改成想要的风格。

## Jekyll 本地预览

页面修改完了，本地看下效果比较方便，所以需要在本地安装jekylly

安装 Jekyll 主要依赖 ruby 和 bandler

#### 1.  Ruby

mac 自带 ruby，但是版本太低不能用，Jekyll 至少要 ruby@2.7

mac 安装那必须是 brew 大法好。 ( ps : 自己搞才知道之前百度的基建做的有多好，啥都有，网速还快)

brew 使用的是清华的镜像，最开始考虑无脑 `brew install ruby`。但是直接 install 版本太新了，需要很多的依赖，家里电脑 2core i5，真心编不动 llvm，编了 5 个小时吧，失败了。最后用了 ruby@3.1。

安装完成后，因为 brew 安装的位置不是在 `/usr/local/bin`，所以需要自己在 PATH 里配置一下环境变量，并且source 生效，source 后再看 version就是3.1了

Tips：install 会显示 dependencies。这时候最好按照这个 list 手动一个一个 install，要不万一中途哪个失败了，又要去 download rb 文件，非常非常耗时。

#### 2. bundler

直接用 gem 安装 bundler 和 Jekyll 即可

ps：ruby@3.x 以上版本，需要在 Gemfile.lock 中做一些特殊的配置 `webrick (~> 1.9)` ，才可以正常使用

### 致谢

博客的模板和排版的中文排版要求参考如下：

<https://github.com/mzlogin/mzlogin.github.io>

<https://mazhuang.org/wiki/chinese-copywriting-guidelines>

感谢大佬！ 



