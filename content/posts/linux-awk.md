---
title: "Linux Awk 入门（01）"
date: 2021-04-21T12:00:28+08:00
categories:
  - 专题
  - 教程
tags:
  - "GNU"
  - "Linux"
  - "Awk"
---

![gun-awk](/static/linux-awk/gnu-awk.png)

# 快速入门 awk

## 一、 awk 的作用、优势和特点

awk 最基础的功能是用作搜索 文件、命令行输出中特定的文本。然后对找到的文本执行指定的操作。

它最大的优势是，它是 linux 等 类 unix 系统下通用的工具，无需额外安装运行时和相关依赖即可使用。而且它 “好读”，“好写”  : P。

> awk is：
>
> refreshingly easy to read and write -- [GNU awk get started](https://www.gnu.org/software/gawk/manual/gawk.html#Getting-Started)

awk 和其他编程语言的主要区别是，它是数据驱动的语言（编程人员指定文本的特征、以及找到后需要执行的操作）。

我对 awk 的理解是，awk 已经不属于我们语言层面上的`语法糖`了，它是我们操作类 unix 操作系统、运维，乃至开发过程中的`语法糖`，是很重要的编程工具，我们应该掌握这个技能。

## 二、基本语句

简单到不能再简单：

``` awk
pattern { action }
pattern { action }
...
```

啥意思？

基本的 awk 程序由一条条表达式组成，每个表达式由 pattern（规则），action（操作）为了和规则区分开来被用 `{}` 包裹。

简单讲一个最简单的 awk 表达式就是： 规则 { 操作 }

## 三、执行 awk 程序


* 直接命令执行一个语句

``` shell
awk 'program' input-file1 input-file2 …
```

* 使用脚本

``` shell
awk -f program-file input-file1 input-file2 …
```

> *注*： 如果使用的是 bash 最好在个人 `启动文件` (如： .bashrc)中加上： `set +H`。见 [Awk env note](https://www.gnu.org/software/gawk/manual/gawk.html#Read-Terminal)

1. 创建一个名为 test-awk.awk 的程序（有没有 awk 后缀其实并不影响，但它使得维护变得更加方便），在其中写好 `BEGIN { print "Don't Panic!" }` ;
2. 然后 `awk -f test-awk`;

这和直接执行 `awk 'BEGIN { print "Don\47t Panic!" }'` 是一样的效果。

> *注*：  在 shell 中，执行的命令中被双引号括起来的文本是会被执行的，例如上文的 “\47”。

## 四、最基础的 awk 程序

一个基础的 awk 程序的结构是下面这样的：

``` awk
BEGIN { ...... }
pattern { action1 }
pattern { action2 }
END { ...... }
```

例如我们可以这样写：

``` awk
BEGIN { print "File\t\tOwner"}
{ print $9, "\t", $3}
END { print " - DONE -" }
```

* 灵魂问题： 这个程序有什么用？ 当然是入门 awk。。。

这个脚本可以被 `ls -l` 直接使用，我们把它放到刚才的 awk-test.awk 里，然后执行 `ls -l | awk -f ./awk-test.awk`

可以看到输出如：

``` shell
File             Owner

HelloWorld.lua   picher
awk-test.awk     picher
connect-dev.sh   picher
find-home.sh     picher
login-dev.sh     picher
ncProxy.sh       picher
pro-emacs.sh     picher
 - DONE -
```

> 忽略一些不相关的东西（doge，我们可以看到，这个脚本就是过滤了我们 ls 命令的输出。


仔细观察这段代码，并试着执行一次就可以学到以下知识点：

* BEGIN 和 END 是重要的起始、结束 awk 脚本的命令；
* pattern 可以没有，默认是使用所有的输入；
* $9 $3 这种语句在 awk 程序中代表的是 pattern 匹配的第 n 列文本；
* shell 的双引号不认识 $ 符号，即 shell 不会处理在 双引号中的 $ 符号；

## 五、awk 程序的执行

awk 程序有一个基本的运行规则，如下图所示：

![gun-awk](/static/linux-awk/awk-flow.png)

* 读取：

awk 会从输入流中读取一行，并存储在内存中（注意，awk 会把读入的内容存储到内存中）。

* 执行：

所有在  begin 和 end 块之间的 awk 语句会被按书写顺序，从上到下对读入的行执行。

默认情况下，awk 会对所有的行执行所有的语句，如果某个文件有 1000 行， awk 语句共 8 行，那么直到 awk 程序结束，实际上会执行 8 * 1000 = 8000 个语句。

当然我们也有特定的方法使得 awk 不处理特定的行。

* 重复：

若没有处理到文件末尾（EOF），awk 继续执行上述的两个步骤。


# 参考资料

[1] [GNU awk guide](https://www.gnu.org/software/gawk/manual/gawk.html)

[2] [awk tutorial](https://www.grymoire.com/Unix/Awk.html#toc_Awk)
