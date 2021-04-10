---
title: "解决 hugo 博客框架下 中文简介、评论、扩展配置 的问题"
date: 2021-04-09T18:53:13+08:00
draft: true
---

## 前言

前段时间把博客主题切换到了 hugo，但同时也遇到了一些需要自己去折腾的问题。

例如，安装的主题貌似对中文支持不太好，安装的主题没有搭配除了 disqus 以外的评论插件怎么办，流量统计怎么做，安装的主题图片展示效果较差怎么办？

这篇文章主要就是为了讨论这个问题。

## 识别问题

以上的问题属于是具体问题。虽然可以具体问题具体分析，但是为了在一篇文章内覆盖更多可能遇到的情况，可以简单的将它们分为以下几类：

1. 修改 hugo 的变量/flag。
2. 如何使用第三方插件。
3. 如何覆盖主题的模块。

下面依次对这三个问题进行分析。

## 修改 hugo 的变量、flag 实现自定义

先来看具体问题：

我遇到的问题是，文章列表下的文章简介过于多，导致列表界面非常杂乱的问题。效果如下：

![列表界面](/static/summary.png)

## 使用第三方插件

第二个问题是如何使用评论插件的问题，主题自带的评论插件是 disqus ，我个人不是很喜欢，主要是广告：P。我更倾向于直接快速的配置，同时可以让使用 github 的同学一起交流。

官方文档中有推荐评论插件，大致如下：

![官方评论文档推荐](/static/hugo-comment-doc.png)

那么我选择的是 utterances 这个插件，这个插件看起来非常轻量，配置也非常简单。

这里简单描述下配置过程：

1. 建立一个公有仓库

我之前使用过类似的插件，应该是叫做 gittalk 还是 gitment 啥的，所以我已经有一个[仓库](https://github.com/Bitnut/comment)了，很好这一步我省去了。

可以看到这个仓库没有代码也只有默认的分支，这是因为这些基于 github issue 的评论插件对实际代码并不关心，它们只是把文章当作 issue ，评论当作 issue 下的回复。

2. 安装 utterances：

github 的评论仓库需要安装 utterances。

具体操作是： 进入 [utterances](https://github.com/apps/utterances) 然后点击 Install 进行安装。

选择我们建立好的空仓库作为下载的库即可。

再次进入 utterances，显示如下：

![new utterances](/static/new-utteraces.png)

点击 configure 再点击自己的头像

![utterances config](/static/utteraces-config.png)

这样安装就算是完成了。

3. 配置文件开启配置

``` toml
[Params.utteranc] # 注意自己的配置写法
  enable = true
  repo = "Bitnut/comment" # 换成自己的
  issueTerm = "pathname"
  theme = "github-light"
```

其中的首行（Params 行）需要注意一下，hugo 的配置是 `大小写敏感` 的，所以，具体怎么写，视主题的配置方式而定。

## 覆盖主题的模块
