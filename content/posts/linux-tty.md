---
title: "Linux Tty"
date: 2021-02-18T16:34:47+08:00
draft: true
---
# linux 下的 tty/pty/pts/ptmx (linux 概念系列)

## tty 是什么？

在类 unix 系统中， tty 可以被看作是一个命令，但在更常见的情形下， tty 被认为是终端的代称。
[tty-jpg](/static/tron.jpg)

## tty 在作为命令的情形

随便打开几个终端看看，可以看到下图：

![tty-jpg](/static/tty-order.png)

可以看到，输出的结果是一段地址，紧接着， cd 到这个地址下查看：

![tty-jpg](/static/tty-location.png)

在 /dev/pts 这个地址下，有几个文件，分别是：

`0 / 1 / 2 / 3 / ptmx`

### 几个文件是啥意思？

在 Linux 系统的设备特殊文件目录 /dev/ 下，终端特殊设备文件一般有:

*注：以下几列中的地址，末尾带有'/'的是目录，否则是一个文件；n 应直接看作数字的代称，如 0,1,2,3......*

* 串行端口终端(/dev/ttySn)
* 伪终端(/dev/pty/)
* 控制终端(/dev/tty)
* 控制台终端(/dev/ttyn, /dev/console)
* 虚拟终端(/dev/pts/n)
* 其他

我们用 tty 命令打印出的结果属于虚拟终端。

### 深入理解 tty 命令关联到的终端设备文件

tty 命令得到的是一个虚拟终端的文件，那么什么是终端什么是虚拟终端呢？

从命名中其实已经可以窥见一二了，一个虚拟终端就是终端的一种而已。而虚拟终端又和伪终端联系紧密。

#### 伪终端

伪终端（pty），其中的 pt 全称是： pesudo terminal。顾名思义，这是一种和实际物理设备相对的概念。它不是实际意义上的串口设备，而且是一套`主从工具`(master-slave, 不知道现在还能不能这么写了......狗头)。

至于为什么 pty 要写成 pty 而不是 pt，这应该是个历史遗留问题，pty 实际上指的是 **pseudo-teletype**， tty 原本也指的是 **teletype**。

* slave: 指的是模拟硬件设备的一端。
* master: 指的是为终端模拟器提供控制 slave 端方法的一端。

可以简单得这样理解，伪终端的出现，为用户提供了一个 终端模拟器 => 伪终端 => 硬件设备 的管道，伪终端抹去了硬件设备的复杂性，提供了相对简单和统一的交互方式。因此上文的说明中也使用了 **一端** 这个说法，也就是（one end）的意思。

在 /dev/ 这个地址下，主从伪终端的命名方式经常是： /dev/pty/mn & /dev/pty/sn，其中的 m 代表 master ，而 s 代表着 slave。

#### 虚拟终端

虚拟终端，就是在 Xwindows 模式下的伪终端。即上文中 pty 的一种。虚拟终端 pts(pseudo-terminal slave)是 pty 的实现方法。


#### ptmx

ptmx 代表的是： pseudoterminal master

注意到，ptmx 同时出现在 /dev/ 和 /dev/pts/ 文件夹下面。我们打印一下这两个文件：

``` shell
picher@pichers-laptop:/dev$ ls -l /dev/ptmx /dev/pts/ptmx
crw-rw-rw- 1 root tty  5, 2 2月  19 21:34 /dev/ptmx
c--------- 1 root root 5, 2 2月  16 17:25 /dev/pts/ptmx
```

从两个文件的 major 和 minor Number： 5, 2; 以及同样的设备类型： c (character or block) 可以得知，这两个设备文件符代表和使用的是内核中的同一个功能。

实际上，在 /dev/ 文件夹下的 ptmx 是标准的实现，而 /dev/pts/ 下的，则是为软件容器（如 docker）添加的一个 master 伪终端。

# 参考资料

* Pseudoterminal
[Pseudoterminal](https://en.wikipedia.org/wiki/Pseudoterminal)

* tty (unix)
[tty(unix)](https://en.wikipedia.org/wiki/Tty_(unix))

* pty(7) — Linux manual page
[pty(7) — Linux manual page4](https://man7.org/linux/man-pages/man7/pty.7.html)

* ptmx(4) - Linux man page
[ptmx(4) - Linux man page](https://linux.die.net/man/4/ptmx)

* some-confused-concept-ptmx-and-tty
[some-confused-concept-ptmx-and-tty](https://unix.stackexchange.com/questions/449315/some-confused-concept-ptmx-and-tty)
