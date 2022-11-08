---
title: "CPU 缓存梳理及探讨"
date: 2022-11-08T08:04:50+08:00
categories:
  - 经典设计
  - 计算机组成
tags:
  - "linux"
  - "hardware"

---

## 概述

本来写这篇文章的出发点是想要梳理一下，工作中常用到的 cache，借此为自己的知识体系做一个总结和深化。但是，总感觉简单地梳理又不那么令人满足，于是打算开启一个 cache 系列，分模块对 cache 进行一个 `TTT 型` 的总结。在这些总结中，每一章都各有自己的侧重点，从不同的角度，归纳和剖析我们日常使用的 cache 技术。

这一章的主要内容是： 借助最为经典和深刻的 cpu cache 架构，试图找到 cache 体系的设计思想和逻辑。

CPU 是计算机世界中几乎是最微观的底层硬件。在现在的计算机架构下，无论我们编写什么样的程序，最后都会交付给 cpu 执行和计算，可以说 cpu 就是计算机组成中核心中的核心。可以看到，CPU 的神奇指出在于，CPU 是一个通用的解决方案，它只把 `计算能力` 抽象出来，只关心不断的`取指`和`计算`，使得 CPU 的地位稳定而且不可动摇。

## 常用缓存梳理

在平时我们常见的缓存大致可以分为`硬件缓存`和`软件缓存` [<sup>1<sup>](#ref-1)。大致如下：

1. hardware

    * cpu cache
    * gpu cache
    * TLB
    * ...

2. software

    * disk cache
    * web cache (nodebb)
    * CDN
    * Cloud storage (s3, cloud)
    * DNS
    * DB cache
    * Distributed cache
    * ...

## cpu 缓存

### 为什么要引入 cpu 缓存

最早期的 cpu 并没有引入 cache，cpu 直接从存储器中读取指令和数据到寄存器中执行。说到底，引入缓存的考量都是基于对性能和速度的渴求。如果没有缓存，cpu 将会一直出于等待数据传输的过程中，大量的 cpu 时钟周期将会被浪费。

各级缓存的延迟数字可以见下图[<sup>2<sup>](#ref-2)：

![storage-latency](/static/storage-latency.png)

还有一张很著名的表格，对应的时间应该是 2012 年，按照实际时间进行排序：

| Work                               | Latency        |
|:-----------------------------------|:---------------|
| L1 cache reference                 | 0.5 ns         |
| Branch mispredict                  | 5 ns           |
| L2 cache reference                 | 7 ns           |
| Mutex lock/unlock                  | 25 ns          |
| Main memory reference              | 100 ns         |
| Compress 1K bytes with Zippy       | 3,000 ns       |
| Send 1K bytes over 1 Gbps network  | 10,000 ns      |
| Read 4K randomly from SSD*         | 150,000 ns     |
| Read 1 MB sequentially from memory | 250,000 ns     |
| Round trip within same datacenter  | 500,000 ns     |
| Read 1 MB sequentially from SSD*   | 1,000,000 ns   |
| Disk seek                          | 10,000,000 ns  |
| Read 1 MB sequentially from disk   | 20,000,000 ns  |
| Send packet CA->Netherlands->CA    | 150,000,000 ns |

虽然说具体数字上会有差异，我们可以看到， L1 的速度大概是 L2 的 7-14 倍，差不多是主存（main memory） 的 1~200 倍，相差两个数量级，这种性能上的差距导致 cpu 的设计者引入了中间缓存来提升 cpu 的利用率。但是从实际的硬件大小上看， L1/L2 是 KB 级别的缓存，L3 一般会是 MB 级别的，主存则是 GB 级别的。

下面介绍两个我们身边的实例来对比一下。

#### cpu 缓存实例

公司的某型号设备，使用的是联咏公司生产的，架构为 `ARM Cortex-A9` 的 soc 芯片，根据官网的介绍，这种架构是：

> The Cortex-A9 processor features a dual-issue, partially out-of-order pipeline and a flexible system architecture with configurable caches and system coherency using the ACP port. The Cortex-A9 processor achieves a better than 50% performance over the Cortex-A8 processor in a single-core configuration.

和 cache 有关的参数有：

| cache 级别 | 容量大小                                               |
|:----------:|:------------------------------------------------------:|
| L1 cache   | 32 KB I, 32 KB D                                       |
| L2 cache   | 128 KB–8 MB (configurable with L2sr1 cache controller) |

使用同样架构的 soc 芯片还有 Apple A5，于 March 11, 2011 发布，搭载在

* iPad 2

* iPhone 4S

* Apple TV (3rd generation)

* iPod Touch (5th generation)

* iPad Mini (1st generation)

* Apple TV (3rd generation Rev A)

#### 怎么查看 cpu 的缓存大小

查看电脑、设备 CPU 的详细信息可以通过以下方式：

* 系统提供的 gui(略)

* 登入 tty，输入命令获取

一般可以通过以下命令获取：

* Linux 系统：

``` shell
$ lscpu
```

* Windows

``` powershell
$ wmic cpu get caption, deviceid, name, numberofcores, maxclockspeed, status or systeminfo
```

* 系统文件、运行时数据

在 linux 下，另外的方式是直接查看 /proc 文件夹下的文件：cpuinfo

在 Linux 系统中，这是一个特殊的文件夹，里面包含了 kernal 会用到的信息，比较特别的是里面的“文件” 大小都是 0（除了 kcore, mtrr 和 self）


### 什么是 cpu 缓存

#### cpu 缓存的历史

CPU(central processing unit) 是什么时候诞生的？早在 1955 年，CPU 这个词就已经出现了 [<sup>3<sup>](#ref-3)。但是如果按照 CPU 的定义： 用来执行程序的设备，那么最早 CPU 的历史可以被追溯到 1945 年 6 月 30 日，出现在 `John von Neumann` 发布的论文 `First Draft of a Report on the EDVAC` (*First Draft*) 中 [<sup>4<sup>](#ref-4)。

但是，CPU 在诞生的初期，最顶尖的学者也没有意识到，CPU 需要引入缓存的概念————一直到 `1960s`，人们才为 CPU 引入了缓存，而且这种初级的缓存甚至只有一级(L1)，没有我们今天常见的 L2/L3 级缓存。`L1d/L1i` 缓存的引入也是直到 `1976` 年 (IBM 801) 才出现。演变过程可以见下图：

![cpu-cache-history](/static/cpu-cache-history.png)

#### 一些 cpu 缓存相关的基础知识

* L1 缓存分成两种，一种是指令缓存 (L1-I)，一种是数据缓存 (L1-D)。L2 缓存和 L3 缓存一般不分指令和数据
* L1 和 L2 缓存在每一个 CPU 核中，L3 则是所有 CPU 核心共享的内存

其他的一些存储系统如下图[<sup>5<sup>](#ref-5)所示：

![storage-p](/static/storage-p.png)

> Q

看到这里有一个不明显但是值得思考的问题是，同样是 `SRAM` 类型的缓存芯片，为什么 L1 的速度要比 L2/L3 的缓存快那么多？


> A

可以从三个角度来解释这个问题：

1. 空间排布

首先从物理角度上来说，在同样的介质中传输同样的物体需要消耗的时间，会随着传输距离的增大而增加；L1 cache 的分布位置比起 L2/L3 距离 CPU 更近。


其次信号在芯片、晶体管中传输的实际速度远小于`光速`，这个速度大概是： 30cm/ns。所以在一块芯片大小的空间上，位置影响到了实际的传输延迟。

2. 容量大小

L2 的容量要大于 L1 ，如果要构建更大容量的芯片，实际上就需要更多的晶体管，这起始也影响到了 `距离` 这个参数，因为访问最远的晶体管变得更慢了，因为信号需要传输的路径变长得更长了。

3. 密集程度

更小容量的缓存，可以设计为制造的时候采用更大的晶体管，提升芯片的整体性能。

我们来看一张 `VIA Isaiah` 处理器的结构图，如图所示：

![via-chip](/static/via-chip.jfif)

* cache-line/cache-entries

来了解一下 cpu 缓存中的存储方式，这里需要明白一个术语 `cache-line`，这个东西其实和我们学到的其他计组硬件的所谓 `块`、`页`差不多，实际上就是等量的对整个芯片的容量进行划分。


比如说，我们的 cpu 采取的是大小为 64B 的 `cache-line`，那么一个大小为 32KB 的 L1D 所存储的`cache-line` 个数就是： 32KB/64B = 512 个 。


除了对容量进行划分，还有一个关键点就是，如何寻址。实际上寻址可以看作是 `数据块` 的 tag、key，这和我们经常使用的 map，redis 里面的 key 索引，sql 里面的主键索引，起到的作用一模一样，就是需要根据这个 key，找到对应的数据。这就是另一个 cache 的关键知识点：地址关联。

具体的地址关联知识不在这里继续展开了，感兴趣的朋友可以自行了解(复习)。

### 多级缓存引入的问题

多级缓存看上去是一个很美好的实现，每一层存储介质都是下一层的缓存，只要找不到就继续往下一层找。但是现实是残酷的，开发过程往往是按下葫芦起了瓢。多级缓存带来了高性能，也引入了下面两个问题：

* 缓存 miss 的处理问题
* 缓存一致性问题（多核）

后半部分会就 `缓存 miss` 的问题展开进行阐述。

### CPU 中的缓存更新实践

#### Read-Through

前文有讲到，CPU 会采取 `read-through` 的策略读取数据，降低时延。我们主要来讲发生 `缓存 miss` 的时候，需要采取什么样的操作。


如果是读取的时候发生 `cache miss`，CPU 就会试图向更低一层的 cache 读取数据，读取到的数据会被引入到当前缓存中，这个时候就需要 `淘汰算法` （Replacement policies） 来处理。


淘汰算法主要有两种，一种是随机淘汰，一种是 LRU（least-recently used）。


#### Write-Through

当 CPU 执行了写操作的时候，从 cache 这一层存储层一直到硬盘都需要更新。这个时候有两种更新策略可供选择： `write-through` 和 `write-back`。我们先来讲前者。




## 几点启发

### 成本和性能的权衡

### 缓存更新的最佳实践


## 小结


## 文献引用

<div id="ref-1"></div>

- [1] [wiki-Cache]( https://en.wikipedia.org/wiki/Cache_(computing))

<div id="ref-2"></div>

- [2] [storage-interactive-version](https://colin-scott.github.io/personal_website/research/interactive_latency.html)


- [3] [Teach Yourself Programming in Ten Years](http://norvig.com/21-days.html#answers)

<div id="ref-3"></div>

- [3] [wiki-CPU-history](https://en.wikipedia.org/wiki/Central_processing_unit#History)

<div id="ref-4"></div>

- [4] [First Draft of a Report on the EDVAC](https://en.wikipedia.org/wiki/First_Draft_of_a_Report_on_the_EDVAC)

<div id="ref-5"></div>

- [5] [深入理解计算机系统]()
