---
title: "如何简单粗暴地让网页变黑白？"
date: 2020-04-14T22:31:55+08:00
tags:
  - "web"
  - "css"
  - "frontend"
---

很久没有更新博客了，而且前段时间也在忙着找工作，到现在才新入职两天T.T，今天开始重新更新个人博客了。

#### 今天主要来探讨一下让页面变黑白效果的方法


今年早些时候，新冠疫情给国内造成了巨大损失。因此在今年四月四的时候，为了叨念逝者、寄托哀思，国内不仅暂停了大部分娱乐活动，而且各大门户网站、视频网站，都对首页或者所有页面做出了内容和样式的大调整。

在诸多修改中，最具视觉冲击力的当属大幅的网页黑白效果了。那么，如何不修改网页的大多数属性、重新打包图片等，简单地实现页面的黑白效果呢？

#### 可以借用 CSS3 的 filter滤镜属性

最直接的想法应该就是修改CSS样式表了。通过简单的查询可以了解到，CSS3中有filter这个属性。

* grayscale(%)
将图像转换为灰度图像。值定义转换的比例。值为100%则完全转为灰度图像，值为0%图像无变化。值在0%到100%之间，则是效果的线性乘子。若未设置，值默认是0；

简单的用法：
```
img {
    -webkit-filter: grayscale(100%); /* Chrome, Safari, Opera */
    filter: grayscale(100%);
}
```
上面的代码通过 img 选择器将所有图片改成了黑白色

如果想要将整个页面改成黑白色，可以选择使用 html 选择器。

```
html {
    -webkit-filter:grayscale(100%);
    filter: grayscale(100%);
}

```

具体可以到菜鸟教程里查看filter这个属性的各种滤镜，功能可以说是非常强大。

[CSS3 filter(滤镜) 属性](https://www.runoob.com/cssref/css3-pr-filter.html)

但是问题就在于这是一个CSS3属性。兼容问题目前始终是 CSS3 绕不开的问题，我们通过[can i use](https://caniuse.com)查阅，可以看到该属性对于IE等老旧浏览器并不友好。

![filter 属性的兼容情况](https://upload-images.jianshu.io/upload_images/7277397-b037d456fbfc862e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![greyscale 等的兼容情况](https://upload-images.jianshu.io/upload_images/7277397-1b6c3ee07aa692ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前查询到的能兼容其它浏览器的较全版本如下所示：

```
html{
    filter: gray;
    filter: grayscale(100%);              /* 标准写法 */
    -webkit-filter: grayscale(100%); /* webkit内核支持程度较好 */
    -moz-filter: grayscale(100%);
    -ms-filter: grayscale(100%);
    -o-filter: grayscale(100%);
    filter: url("data:image/svg+xml;utf8,<svg xmlns=\'http://www.w3.org/2000/svg\'><filter id=\'grayscale\'><feColorMatrix type=\'matrix\' values=\'0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0\'/></filter></svg>#grayscale");
    filter: progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
    -webkit-filter: grayscale(1);
}

```

前面的几段很普通，是平常的兼容性写法。

`filter:url("data:image/svg...` 这一行有些意思，它主要针对的应该是Firefox浏览器，因为 Firefox 虽说不支持css filter，但是支持svg effects for html。算是曲线救国了～

 `svg` 图片进行解码与颜色填充达到修改颜色的目的。

而`filter:progid:DXImageTransform.Microsoft.BasicImage()`此行代码为IE自己提供的滤镜实现，性能不太友好。

可以来看看腾讯视频、哔哩哔哩等对于黑白效果的实现。

哔哩哔哩：仅一行代码，图片素材则是单独替换的灰度图，并非通过样式修改，应该是有性能上的考量：

```
html{
    webkit-filter: grayscale(.95);
}

```

腾讯视频使用代码如下，在IE浏览器下同样表现良好：

```
html{
    filter: gray;
    -webkit-filter: grayscale(100%);
    filter: grayscale(100%);
}

```

总的来说，上面的方式可以解决大部分的问题，flash、视频等内容没办法一一覆盖，像B站就只是更改了首页的色调，其他的页面并没有做特殊处理。

所以具体情况还是要具体分析。对于大型的样式管理比较复杂的网站，要是因为这样一个小调整导致整个网站访问大幅减慢，身为做出微小工作的你，可能就要背锅了2333。

最后对于IE10、11，可能还得用js。解决方案主要参考ajaxblender的《Convert Images to Grayscale》。

来做个小实验吧。

在 App.vue 中的 style 标签中插入上述代码。

原来的样子：

![一个新的 vue 项目](https://upload-images.jianshu.io/upload_images/7277397-52f9a47cd114bff1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

插入后，在chrome和Firefox中的样子：

![黑白处理之后](https://upload-images.jianshu.io/upload_images/7277397-e56c81fa1064f5dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

简单记录一下～
