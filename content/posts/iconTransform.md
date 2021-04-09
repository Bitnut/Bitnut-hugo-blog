---
title: "解决按钮移动动画伴随光标样式抖动的问题"
date: 2020-04-30T00:01:55+08:00
tags:
  - "web"
  - "css"
  - "frontend"
---

### 问题描述

最近在工作中遇到了一个很奇怪的问题，网站的页面大部分按钮和卡片都有一个向上移动的过渡动画，当鼠标悬浮在这些元素上的时候，动画会触发并在一定时间内缓缓完成、同时鼠标样式变成 pointer，。

但是问题在于这个效果的实现并不理想。鼠标要是从左右两边和上边移入按钮或者卡片的话这个效果是看不出啥问题的，要是鼠标是从下往上缓缓进入，或者停留在上移的距离内，这个特效会导致鼠标和按钮/卡片样式不断抖动，非常鬼畜。具体效果不便直接截图给大家展示，但是我在下面给出了解决方案后会给出一个模拟的效果 gif 和代码（gif制作中）。


### 问题解决

虽然这个问题不在我的工作职责范围内，但是作为一个“前端”工程师，这个问题真的唤醒了我的强迫症，不能忍啊！有没有。

但是又因为本人是一个真实菜鸡前端，css方面急需恶补那种。。。刚遇到这个问题连怎么实现元素上移都不知道T.T。所以一时间对这个问题实在想不出啥解决方法。然而在几天之后，我在休息的时候解决了这个问题。

其实解决方法很简单，其实就是原本代码实现移动效果使用的是css的top属性结合position: relative；要解决这个问题只需要把实现方式更换成transform并使用相应的transform函数即可，无需结合relative布局。

下面给出情景模拟的代码，大家可以拷贝到一个 html 文件里看看是啥效果。


    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Document</title>
    </head>
    <style>
    body {
        text-align: center;
    }
    #head {
        height: 100px;
        width: 100%;
    }
    #middle {
        margin: 0 auto;
    }
    #top-button {
        top: 0;
        position: relative;
        transition: .5s;
        -moz-transition: .5s;
        -webkit-transition: .5s;
        -o-transition: .5s;
        -ms-transition: .5s;
        cursor: pointer;
    }
    #top-button:hover {
        top: -8px;
    }

    #transform-button {
        margin-left: 100px;
        transition: .5s;
        -moz-transition: .5s;
        -webkit-transition: .5s;
        -o-transition: .5s;
        -ms-transition: .5s;
        cursor: pointer;
    }

    #transform-button:hover {
        transform: translateY(-20%);
    }
    </style>
    <body>
        <div id="head"></div>
        <div id="middle">
            <button id="top-button">top 实现</button>
            <button id="transform-button">transform 实现</button>
        </div>

    </body>
    </html>

### 问题深究

为啥使用不同的 css 属性会导致如此巨大的效果差异呢？

我突然意识到 css 其实是在网页中其实属于较高层次的抽象，出现问题后难以纠错主要是在于使用者对 css 的了解太过于浅薄，往往知其然不知其所以然，仅仅停留在使用的层次。因此出现问题和解决问题的过程宛如魔法一般。

所以这次我们就借着遇到的这个问题，来探讨一下css渲染web画面的一些底层原理：

---------------------------------

#### 浏览器如何绘制 dom 元素？

注意我们讨论的是绘制的步骤，在绘制之前，还会经过css计算、布局等步骤。

1. 获取 DOM 并将其分割为多个层（layer）
2. 将每个层独立地绘制进位图（bitmap）中
3. 将层作为纹理（texture）上传至 GPU， GPU 会复合（composite）多个层来生成最终的屏幕图像，然后将其显示在屏幕上。

具体的细节有可能因为 webkit 的不同而产生区别。

#### top 和 transform 的根本区别？

top 是一个布局属性，而 transform 是一个触发合成层的属性，它并不会在布局阶段被使用。

所以现在明白了，为啥使用top实现的动画要配合relative实现？因为它是一个布局属性，一旦它处于文档流中，还能够对其他元素造成影响，这样会造成整个页面回流（reflow）和重绘。

所以使用top的动画功能的时候需要配合 relative、absolute 这些属性。

但是无论如何，使用top做动画特效实际上都会触发布局计算和重绘过程，生成新的帧画面后，交给 GPU 去合成图像，这样实际上造成了额外的性能开销和不必要的动作；而transform只是会额外地生成一个合成层，动画元素会在一个独立的层次中动画，并直接交由 GPU 处理，没有经过重绘来生成新的帧。


#### 还有什么属性会触发合成层？

* 3D 或透视变换 CSS 属性
* 使用加速视频解码的 <video> 元素
* 拥有 3D (WebGL) 上下文或加速的 2D 上下文的 <canvas> 元素
* 复合插件(如 Flash)
* 进行 opacity/transform 动画的元素
* 拥有加速 CSS filters 的元素
* 元素有一个包含复合层的后代节点(换句话说，就是一个元素拥有一个子元素，该子元素在自己的层里)
* 元素有一个 z-index 较低且包含一个复合层的兄弟元素(换句话说就是该元素在复合层上面渲染)
--------------------------------------

### 总结一下
1. 对布局属性进行动画，浏览器需要为每一帧进行重绘并上传到 GPU 处理。
2. 对合成属性进行动画，浏览器会为元素创建一个独立的复合层，该层不会被重绘，直接交由 GPU 计算图像。
