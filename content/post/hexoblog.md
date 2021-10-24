---
title: "Hexo+GithubPages 非常简单易行的搭建个人博客"
date: 2018-07-29T09:45:55+08:00
tags:
  - blog
categories:
  - 工具
---

![书写](https://upload-images.jianshu.io/upload_images/7277397-e35bec7c84c6a4aa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 前言：搭建博客要自己打代码吗？
* 开始动手：搭建博客的步骤
* 个性化：更换主题！！
* 写博客：初识 markdown 语法
* 倒腾主题
****
## 前言
### 搭建博客难吗？
看完这篇你就知道有多简单 :P

### 搭建个人博客需要自己打代码吗？

想要美美哒，肯定是要一点的啊:disappointed_relieved:

但是说白咯，搭建博客和做程序一个道理：个人的时间和精力是有限的，一个程序往往都是使用了一堆别人的东西，来完成一个属于你自己的作品。没有人能够有那样的经历和智慧在建一座砖瓦房之前研究烧砖、搭瓦，建造，结构力学等等等等。

我们所需要完成的是站在巨人们的肩膀上看远一点，想深一点。

搭建我们的这样一个博客需要用到的主要配件：[Hexo](https://hexo.io/zh-cn/)、[Hexo 主题](https://hexo.io/themes/)、[Git](https://git-scm.com/)、[GithubPages](https://pages.github.com/)、[Gitment](https://github.com/imsun/gitment)，这些都不需要自己去独立完成。而且这些都是免费的，除了买域名需要少许的钱。或者你执意要搭在自己的服务器上，当然这个从价格的计算上就不能一概而论了:（

总之搭建自己的博客无论从价格上，还是说耗费的时间和精力上都是微不足道的。毕竟，人家设计好的东西，我们只要直接拿来用就可以了。


### 概念简要介绍：

* Github：这个必须有，无论是文字工作者还是程序员都得有一个，全世界人民认可的好东西。当然搭建博客你甚至不需要会操作它。但是谁知道你会不会突然就学会了呢？？ :smiling_imp:
* Git：Git是目前世界上最先进的分布式版本控制系统。:+1:
* Hexo：Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Heroku上，是搭建博客的首选框架之一。
* GithubPages：是Github推出的功能，只要你建立了github.io这个特殊的仓库库，github 就默认你在使用它。它相当于一个服务器，可以保存你的所有博客文件，类似电脑上博客站点的一份备份。


当然，搭建博客时你可以什么都不懂。但为了以后能更好的操作你的博客，建议在搭建成功之后，好好学习一下 Git 和 github 的使用。初步了解可以通过[廖雪峰大大的博客](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。


### 为什么一个人需要写博客？？

这个问题建议你看看这篇文字：[为什么你要写博客？](https://zhuanlan.zhihu.com/p/19743861?columnSlug=cnfeat)。

虽然你可以什么都不懂，但以下3样东西你必须要有：

耐心：搭建博客是一件非常折腾的事情，所以耐心很重要
细心：一定要细心，确保每一步都是正确的。
一定的学习能力和钻研精神，遇到困难一定要面对它，主动解决。


### 搭建博客需要多长时间？
可能每个人都不太一样:smile:，这里我给出几个层次：

半天：只是搭建，不涉及到换域名和个性化，并且了解背景知识。那么只要半天便可以搭建完成
一天：需要了解背景知识，并且换了域名和主题，但没有太多的个性化
两天：设置评论，字数统计等
三天甚至更多：各种找个性化插件和设置，一直在折腾的人
当然，前两种都是你没有遇到太大的坑或者困难所给出的估计时间。这种就比较折腾了。我个人是不太推荐的:boom:，因为你实际上把太多精力放在倒腾博客页面而不是内容上了。始终要记得写博客才是重点呢~~~


*****
## 搭建个人博客的步骤
给出我博客的地址吧~[点击这里](https://bitnut.github.io/)
### 一、GitHub创建个人仓库

登录到GitHub,如果没有GitHub帐号，使用某个邮箱注册一个 GitHub 帐号：点击个人页面中的 New repository 创建新仓库。

仓库的名字设定为：自己注册时的用户名 +.github.io
例如
> 我的用户名为：Bitnut
> 那么我的仓库名字就是：Bitnut.github.io

可见这个仓库是每个账号只能创建一个的*特殊类型仓库*

至此，你就已经相当于给自己的博客利用 github 的服务器搭了一个后台。很简单对不对~~

* 注意几点：
1. 注册的邮箱一定要验证，否则不会成功；
2. 仓库名字必须是：username.github.io，其中username是你的用户名；
3. 仓库创建成功不会立即生效，需要过一段时间，大概10-30分钟，或者更久，我的等了半个小时才生效；

### 二、安装Git

> 为什么要安装它呢？因为在 win 下其他的类 Linux、Unix 的 git shell 可能会在用的时候出毛病，用 git 官方的能够避免这类问题。

从Git官网下载：
> [Git - Downloading Package--win](https://git-scm.com/download/win)
> [Git - Downloading Package--mac](https://www.git-scm.com/download/mac)
> [Git - Downloading Package--linux](https://www.git-scm.com/download/linux)

下载安装完成后，在命令行里输入 `git -v` 看看版本并测试是否安装成功.

若安装失败，参看其他详细的 Git 安装教程。

### 三、将你本地的 Git 与 GitHub 帐号绑定。


右键唤出 Git Bash。或者在菜单里搜索 Git Bash，设置 user.name 和user.email 配置信息：

> git config --global user.name "你的GitHub用户名"
> git config --global user.email "你的GitHub注册邮箱"

#### 生成ssh密钥文件：

为什么要配置这个呢？因为你提交代码肯定要拥有你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。

> cd ~/. ssh  #检查本机已存在的ssh密钥

如果提示：No such file or directory 说明你是第一次使用git。

生成密匙：
> ssh-keygen -t rsa -C "邮件地址"

然后连续3次回车，最终会生成一个文件在用户目录下
> ls -a  #直接查看所有文件
> cat .ssh\id_rsa.pub  #直接终端打开文件即可

复制里面的内容，打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：
![ssh-key](https://upload-images.jianshu.io/upload_images/7277397-9f6d25167e1f2d23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



Title为标题，任意填即可，将刚刚复制的id_rsa.pub内容粘贴进去，最后点击Add SSH key。

#### 测试是否成功添加公钥
在 Git Bash 中检测 GitHub 公钥设置是否成功，输入 ssh git@github.com

如果提示`Are you sure you want to continue connecting (yes/no)?`，输入yes，然后会看到：

> Hi Bitnut! You've successfully authenticated, but GitHub does not provide shell access.

如上则说明成功。这里之所以设置GitHub密钥原因是，通过非对称加密的公钥与私钥来完成加密，公钥放置在GitHub上，私钥放置在自己的电脑里。GitHub要求每次推送代码都是合法用户，所以每次推送都需要输入账号密码验证推送用户是否是合法用户，为了省去每次输入密码的步骤，采用了ssh，当你推送的时候，git就会匹配你的私钥跟GitHub上面的公钥是否是配对的，若是匹配就认为你是合法用户，则允许推送。这样可以保证每次的推送都是正确合法的。


### 安装Node.js

由于 Hexo 基于 Node.js 因此我们到官网上下载一下 node.js。

Node.js下载地址：[Download | Node.js](https://nodejs.org/en/download/)
下载安装包，安装Node.js会包含环境变量及npm的安装，安装后，检测Node.js是否安装成功，在命令行中输入 ` node -v ` 检测npm是否安装成功。

* 如果 git bash 里面报错，到环境变量里面看看有没有 nodejs。没有的需要把nodejs的地址添加一下。如果已经有了，重启电脑应该就 OK。

至此，安装Hexo的环境全部配置完成。:clap::clap:


### 安装Hexo

Hexo就是我们的个人博客网站的框架，这里需要自己在电脑常里创建一个文件夹。

*注意不要在这个文件夹下面试用 Git 创建仓库，因为后面安装的主题内可能会带有 git 仓库，这样会导致包管理的混乱。*

可以随意命名，这里命名为 Blog，Hexo 框架和以后我们自己发布的网页都在保存在这个本地文件夹中。创建好后，不需要进入文件夹中，在文件夹的父目录直接唤出 Git Bash。

使用npm命令安装Hexo，输入：

> npm install -g hexo-cli

初始化我们的博客，输入：

> hexo init Blog

注意，这里的命令都是作用在刚刚创建的Blog文件夹中。进入到文件夹中
查看一下我们的目录结构：

![目录](https://upload-images.jianshu.io/upload_images/7277397-a916f2c6b2d400e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


为了检测我们的网站雏形，分别按顺序输入以下三条命令：

> hexo new test_my_site #创建一篇新文章
> hexo g                #生成文件
> hexo s                #本地 server 部署

这样我们在本地成功部署了我们的博客
打开浏览器输入地址：

localhost:4000

## 厉害了小:fire:汁

#### 常用的Hexo 命令

> npm install hexo -g #安装Hexo
npm update hexo -g #升级
hexo init #初始化博客

命令简写
> hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署

> hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令

刚刚的三个命令依次是新建一篇博客文章、生成网页、在本地预览的操作。

### 把博客的更新部署到 Github 上

上面只是在本地预览，接下来要做的就是就是推送更新，也就是发布网站，让我们的网站可以被更多的人访问。在设置之前，

需要区分一下两个概念：

* 在 Blog 根目录里的_config.yml文件称为站点配置文件
* 进入根目录里的themes文件夹，里面也有个_config.yml文件，这个称为主题配置文件




下一步将我们的Hexo与GitHub关联起来，打开站点的配置文件_config.yml，翻到最后修改为：

> deploy:
type: git
repo: 这里填入你之前在GitHub上创建仓库的完整路径
branch: master

![部署](https://upload-images.jianshu.io/upload_images/7277397-5f03a6394dc2d475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


保存站点配置文件。

以上的所有操作其实是在给 `hexo d` 这个命令做相应的配置，让 hexo 知道你要把 blog 部署在哪个位置，很显然，我们部署在我们GitHub的仓库里。最后安装Git部署插件，输入命令：

> npm install hexo-deployer-git --save

完成后，我们分别输入三条命令：

> hexo clean
hexo g
hexo d

第三条的 hexo d 就是部署网站命令，d是deploy的缩写。完成后，打开浏览器，在地址栏输入你的放置个人网站的仓库路径，即 `http://xxxx.github.io` 。其中的 xxxx 就是的GitHub用户名：

你就会发现你的博客已经上线了，可以在网络上被访问了。

* 注意：如果你前面操作的很快，那么你到这一步有可能没法访问，因为github那边给你的网站部署需要花费一些时间，大概需要最长半个小时的时间。


## 更换主题！！！

默认主题:neutral_face:emmmm，不是很好看。所以我们进行到了至关重要的一步！要知道，帅。是一辈子的事情~~~颜值不够看，看ni:horse:呢对吧？

个人最欣赏的主题：[hexo-theme-yilia](https://github.com/litten/hexo-theme-yilia)
这个主题兼顾了颜值和效率，同时在手机端可以有非常好的体验，因此我选择了它。

当然你也可以选择其他主题。主题传送门：[Themes](https://hexo.io/themes/) 我自己使用的是 yilia 主题，可以在blog目录中的themes文件夹中查看你自己主题是什么。


首先下载这个主题：
在根目录唤出 Git Bash,
> git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

这是将 yilia 主题下载到 Blog 目录的 themes 主题下的 yilia 文件夹中。打开站点的_config.yml配置文件，将里面的 `theme: landscape` 改为 `theme: yilia`，然后重新执行 `hexo g` 来重新生成。

如果出现一些莫名其妙的问题，可以先执行 `hexo clean` ，可以清理 public 文件夹里面的内容。然后重新生成和发布即可。


## 初识Markdown语法

Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。Markdown语法简洁明了、容易掌握，而且功能比纯文本更强，因此写博客使用它，可以让用户更加专注的写文章，而不需要费尽心力的考虑样式，相对于html已经算是轻量级语言，像有道云笔记也支持Markdown写作。并且Markdown完全兼容html，也就是可以在文章里直接插入html代码。比如给博文添加音乐，就可以直接把音乐的外链html代码插入文章中。具体语法参看：Markdown 语法说明(简体中文版) 可以说十分钟就可以入门。当然，工欲善其事必先利其器，选择一个好的Markdown编辑器也是非常重要的，这里推荐MarkPad 和The Markdown Editor for Windows ，这是带有预览效果的编辑器，也可以使用本地的文本编辑器，更多的Markdown的语法与编辑器自己可以搜索了解。



发布文章

我们开始正式发布上线博客文章，在命令行中输入：

hexo n "博客名字"


我们会发现在blog根目录下的source文件夹中的_post文件夹中多了一个 博客名字.md 文件，使用Markdown编辑器打开，就可以开始你的个人博客之旅了，Markdown常用的样式也就十来种，完全能够满足一般博文的样式要求，这是我的一篇博文内容示例：




通过带有预览样式的Markdown编辑器实时预览书写的博文样式，也可以通过命令 hexo s --debug 在本地浏览器的localhost:4000 预览博文效果。写好博文并且样式无误后，通过hexo g、hexo d 生成、部署网页。随后可以在浏览器中输入域名浏览。


## 倒腾主题

下面是我在倒腾 yilia 主题的时候遇到的一些坑

首先这个主题是国人写的，所以基本没有一些设置跟着主题里的配置文件的注释走就好。

但是一些小问题其实没有被直接在注释里面提到的，可能会给你造成一些麻烦，这里我列举一些。

### 设置打赏还有自己的头像
图片请保存在主题的 /source/img 下，但是在配置文件里面写的时候注意了，要写成 `/img/zhifubao.jpg`，具体原因主要还是涉及到地址的访问问题，因为每次访问默认都会到 /theme/source 下寻找，所以重复填写 /source 可能会导致图片加载不出来的小问题。

### 设置 favicon
收藏夹图标设置，这个可以到在线的网站上直接生成一个：[favicon制作](https://tool.lu/favicon/)，然后在主题中设置 favicon 项即可。

### aboutme想写长一点怎么办？
没问题，在你想换行的地方添加 `<br>` 就好。
以我自己的为例：
> `aboutme: 是个正在考研的选手<br>性别男，爱好女<br><br>热爱体育、美食、读书、旅游、美女等等<br><br>对金融很感兴趣<br>本科研究过区块链和网站开发。`

### 微信分享那里会出现一些问题
好像本来主题里百度的二维码生成 api 没起作用呢。

现在很多大网站都有这样的一个功能，使用手机扫描一下网页上的二维码便可快速在手机上访问网站。在自己的博客里面想要实现这样的功能其实很简单，下面分享几个在线生成网址二维码的API接口。都是可以采用http协议接口，部分可以使用https协议，无需下载安装什么软件，可简单方便地引用，是最简单、最便捷的免费网址二维码生成工具。

* 推荐的几个在线生成网址二维码的API接口：

1. 百度网盘(可使用https)
`http://pan.baidu.com/share/qrcode?w=150&h=150&url=`
2. iClick接口 (无https)
`http://bshare.optimix.asia/barCode?site=weixin&url=`
3. JiaThis 接口(无https)
`http://s.jiathis.com/qrcode.php?url=`
4. 联图网(无https)
`http://qr.liantu.com/api.php?text=`
5. K780数据网(支持https和http)
`http://api.k780.com:88/?app=qr.get&data=`
6. QR Code Generator(https接口)
`https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=`

* 使用说明：
1. 打开 Chrome 或者其他浏览器，点击页面里的分享中的微信图标，邮件点击弹出窗口，点击`检查元素`或`检查`或者`审查`等等，看个人的浏览器版本。查找到对应的 HTML 中的代码行。
2. 复制里面任何一段代码，用某个 IDE 或者编辑器(我自己使用的是 vscode)，利用代码查找功能查找到对应的代码。
3. 把网站替换为以上的任意一个试试看。会有延迟，多刷新几次试试看。

完成

### 让博客网址使用全英文路径
上面的微信分享二维码功能完成之后，手机扫码出来却不能访问，为什么呢？？

*原因在于你的文章可能使用了中文命名，而 hexo 默认使用了文章标题作为网址*

解决方案：修改 permalink 变量

在博客根目录的配置文件（_config.yml）中这样修改：

> permalink: :year/:month/:day/:title # 这是原配置
> permalink: :year/:id/ # 替换为此新配置

并且在.\scaffolds\post.md中修改为：

* （注意新增了一个id:）

> title: {{ title }}
> id: {{date}}
> date: {{ date }}
> tags:

这里我的 id 初始化的时候是日期，包含年月日时分秒。为了美观和便于识别，我在上传的时候会修改这个 id 使他保证和其他文章不冲突。

我的做法是使用创建日期中的月+日+时+分+秒来定义一个id，比如`月日-时分秒` 。

### 评论功能添加
大家可以去看看，我最后采用的是 Gitment,十分美观可用性强。
主要步骤有：
1. 注册 OAuth Application [点击此处注册](https://github.com/settings/applications/new)。其他内容可以随意填写，但要确保填入正确的 callback URL（一般是评论页面对应的域名，如 https://imsun.net）。通过这个步骤你会得到一个 client ID 和一个 client secret。
2. 在配置文件里面填写相应的内容，下面的都填上~

    gitment_owner: 'Bitnut'        #你的 GitHub ID
    gitment_repo: 'comment'        #存储评论的 repo,注意是名字不是地址！
    client ID
    client secret

评论的其他的一些坑点我参考了[这篇文章](https://www.jianshu.com/p/57afa4844aaa),有问题注意上去看看！


### 最后我给出我自己的配置文件

    # Header

    menu:
    主页: /
    随笔: /tags/随笔/
    金融: /tags/金融/
    计算机: /tags/计算机/
    SCUT: /tags/SCUT/

    # SubNav
    subnav:
    github: "https://github.com/Bitnut/Bitnut.github.io"
    weibo: "https://weibo.com/u/5724174787/home"
    rss: "#"
    zhihu: "https://www.zhihu.com/people/lalalapc/activities"
    #qq: "#"
    #weixin: "#"
    #jianshu: "#"
    #douban: "#"
    #segmentfault: "#"
    #bilibili: "#"
    #acfun: "#"
    #mail: "mailto:litten225@qq.com"
    #facebook: "#"
    #google: "#"
    #twitter: "#"
    #linkedin: "#"

    rss: /atom.xml

    # 是否需要修改 root 路径
    # 如果您的网站存放在子目录中，例如 http://yoursite.com/blog，
    # 请将您的 url 设为 http://yoursite.com/blog 并把 root 设为 /blog/。
    root: /

    # Content

    # 文章太长，截断按钮文字
    excerpt_link: '继续'
    # 文章卡片右下角常驻链接，不需要请设置为false
    show_all_link: '展开全文'
    # 数学公式
    mathjax: false
    # 是否在新窗口打开链接
    open_in_new: false

    # 打赏
    # 打赏type设定：0-关闭打赏； 1-文章对应的md文件里有reward:true属性，才有打赏； 2-所有文章均有打赏
    reward_type: 2
    # 打赏wording
    reward_wording: '感谢您的瓜！希望这篇文字能给你带来帮助~'
    # 支付宝二维码图片地址，跟你设置头像的方式一样。比如：/assets/img/alipay.jpg
    alipay: '/img/zhifubao.jpg'
    # 微信二维码图片地址
    weixin: '/img/weixin.jpg'

    # 目录
    # 目录设定：0-不显示目录； 1-文章对应的md文件里有toc:true属性，才有目录； 2-所有文章均显示目录
    toc: 1
    # 根据自己的习惯来设置，如果你的目录标题习惯有标号，置为true即可隐藏hexo重复的序号；否则置为false
    toc_hide_index: true
    # 目录为空时的提示
    toc_empty_wording: '目录，不存在的…'

    # 是否有快速回到顶部的按钮
    top: true

    # Miscellaneous
    baidu_analytics: ''
    google_analytics: ''
    favicon: '/img/guage.png'

    #你的头像url
    avatar: '/img/gua.jpg'

    #是否开启分享
    share_jia: true

    #评论：1、多说；2、网易云跟帖；3、畅言；4、Disqus；5、Gitment
    #不需要使用某项，直接设置值为false，或注释掉
    #具体请参考wiki：https://github.com/litten/hexo-theme-yilia/wiki/

    #1、多说
    duoshuo: false

    #2、网易云跟帖
    wangyiyun: false

    #3、畅言
    changyan_appid: false
    changyan_conf: false

    #4、Disqus 在hexo根目录的config里也有disqus_shortname字段，优先使用yilia的
    disqus: false

    #5、Gitment
    gitment_owner: 'Bitnut'        #你的 GitHub ID
    gitment_repo: 'comment'        #存储评论的 repo
    gitment_oauth:
    client_id: bu ke yi!!          #client ID
    client_secret: bu ke yi!!      #client secret

    # 样式定制 - 一般不需要修改，除非有很强的定制欲望…
    style:
    # 头像上面的背景颜色
    header: '#4d4d4d'
    # 右滑板块背景
    slider: 'linear-gradient(200deg,#a0cfe4,#e8c37e)'

    # slider的设置
    slider:
    # 是否默认展开tags板块
    showTags: false

    # 智能菜单
    # 如不需要，将该对应项置为false
    # 比如
    #smart_menu:
    #  friends: false
    smart_menu:
    innerArchive: '所有文章'
    friends: '友链'
    aboutme: '关于我'

    friends:
    友情链接1: http://localhost:4000/
    友情链接2: http://localhost:4000/
    友情链接3: http://localhost:4000/
    友情链接4: http://localhost:4000/
    友情链接5: http://localhost:4000/
    友情链接6: http://localhost:4000/

    aboutme: 是个正在考研的选手<br>性别男，爱好女<br><br>热爱体育、美食、读书、旅游、美女等等<br><br>对金融很感兴趣<br>本科研究过区块链和网站开发。
