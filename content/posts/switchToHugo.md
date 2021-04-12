---
title: "解决 hugo 博客框架下 中文简介、评论、扩展配置 的问题"
date: 2021-04-09T18:53:13+08:00
tags:
  - "HUGO"
  - "blog"
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

查询到以前的版本是有这个问题，并有 issue 讨论。

[github issue](https://github.com/gohugoio/hugo/issues/1377)

可以看到已经有解决方法了：

在配置文件中添加如下两行自定义 hugo 内置变量：

``` toml
hasCJKLanguage = true # 开启中文、日文、韩文字符识别，默认为 false
summaryLength = 150 # 自定义 summary 字数，默认是 70
```

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

有没有发现，在静态博客的世界里，自定义实际上就是一层层的覆盖而已。上文的组织顺序也是如此，依次从 `预定义变量` -> `配置文件 + 插件`， 最后来到最自由的覆盖方式： `覆盖模块`。

覆盖模块实际上也有几种方式：

1. 覆盖模版文件；
2. js、css 等运行时覆盖；

下文将分这两点展开。

### 覆盖模版文件

主要参考这里： [覆盖模版文件](https://gohugobrasil.netlify.app/themes/customizing/#override-template-files)

大致就是讲在 `项目根目录` 下的 `layouts` 中建立和主题目录下 `layouts` 文件夹中相应的同名文件，hugo 会依据一个 [查找顺序](https://gohugobrasil.netlify.app/templates/lookup-order/)，优先查找到这个同名文件，并使用它而不是主题定义的模版文件。

接上文。

由于我使用的主题 [mainroad](https://themes.gohugo.io/mainroad)，不包含 utterances 的配置，它也是默认支持的 disqus，因此，我需要覆盖一下原来文章页面的模版文件。我使用的主题的文章页面模板位于： `themes/mainroad/layouts/_default/single.html`。因此有了如下步骤。

步骤：

1. 首先在根目录建立文件夹： `layouts/_default/single.html`
2. 在 footer 处（可以是你希望的别的地方）加入 utterances 的启动脚本：

``` html
<footer class="post__footer">
    <script src="https://utteranc.es/client.js"
            repo="Bitnut/comment"
            issue-term="pathname"
            theme="github-light"
            crossorigin="anonymous"
            async>
    </script>
<footer>
```

查看并测试评论插件是否生效：

![评论插件效果](/static/test-comment.png)

Nice.

### js、css 的运行时覆盖

hugo 还允许用户自己编写 js 脚本以及 css 覆盖页面在线上的行为和样式。

而我恰好有个小需求，我博客页面的图片展示实在是惨不忍睹，我希望写个小插件改善这个问题。

#### 开工

这需要上面提到的模板文件覆盖的知识。

首先我们选择需要覆盖的模板文件，这里我继续复用上面提到的 single.html。并在 footer 加上脚本链接：

``` html
<footer class="post__footer">
    ...
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    ...
    {{ range .Site.Params.customJs -}}
    <link rel="stylesheet" href="{{ . | absURL }}">
    {{- end }}
</footer>
```

#### 解释：

我首先通过 cdn 引入了 jquery，便于我操作 dom，然后使用了在配置文件中定义好的 hugo 变量： `customJs`。

在配置文件中这样添加 customJS 变量：

``` toml
...

[Params]
  ...

  customCSS = ["css/custom.css"] # Include custom CSS files
  customJS = ["js/custom.js"] # Include custom JS files
```

在 params 标签下添加 js 和 css 文件的相对路径（相对于根目录下的 static 目录）。

然后快乐地编写脚本即可～

我实现的插件效果：

![看图插件](/static/img-viewer.png)
