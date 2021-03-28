---
title: "Linux Awk"
date: 2021-03-23T13:46:28+08:00
draft: true
---

# 快速入门 awk

## awk 的作用、优势和特点

awk 最基础的功能是用作搜索 文件、命令行输出中特定的文本。然后对找到的文本执行指定的操作。

它最大的优势是，它是 linux 等 类 unix 系统下通用的工具，无需额外安装运行时和相关依赖即可使用。而且它 “好读”，“好写”。

awk is：

refreshingly easy to read and write --[GNU awk get started](https://www.gnu.org/software/gawk/manual/gawk.html#Getting-Started)

awk 和其他编程语言的主要区别是，它是数据驱动的语言（编程人员指定文本的特征、以及找到后需要执行的操作）。

## 基础语法

简单到不能再简单：

``` awk
pattern { action }
pattern { action }
...
```

啥意思？

基本的 awk 程序由一条条表达式组成，每个表达式由 pattern（规则），action（操作）为了和规则区分开来被用 `{}` 包裹。

简单讲一个最简单的 awk 表达式就是： 规则 { 操作 }

## 执行 awk 程序


* 直接命令执行

``` shell
awk 'program' input-file1 input-file2 …
```

* 使用脚本

``` shell
awk -f program-file input-file1 input-file2 …
```

> *注*： 如果使用的式 bash 最好在个人启动文件中加上： `set +H`。见 [Awk env note](https://www.gnu.org/software/gawk/manual/gawk.html#Running-gawk)

1. 创建一个名为 test-awk.awk 的程序（有没有 awk 后缀其实并不影响，但它使得维护变得更加方便），在其中写好 `BEGIN { print "Don't Panic!" }` ;
2. 然后 `awk -f test-awk`;

这和直接执行 `awk 'BEGIN { print "Don\47t Panic!" }'` 是一样的效果。

> *注* 在 shell 中，执行的命令中被双引号括起来的文本是会被执行的，例如上文的 “\47”。

## 最基础的 awk 程序

``` awk
BEGIN { print "File\t\tOwner"}
{ print $9, "\t", $3}
END { print " - DONE -" }
```

* 灵魂问题： 这个程序有什么用？ 当然是入门 awk。。。

实际上这个脚本可以被 ls -l 直接使用，我们把它放到刚才的 awk-test.awk 里，然后执行 `ls -l | awk -f ./awk-test.awk`

可以看到输出：

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
* shell 的双引号是不认识 $ 符号；

## 基本运算符

| 运算符  | 类型       | 涵义 |
|:-------:|:----------:|:----:|
| +       | 数学运算符 | 加法 |
| -       | 数学运算符 | 减法 |
| *       | 数学运算符 | 乘法 |
| /       | 数学运算符 | 除法 |
| %       | 数学运算符 | 取余 |
| <space> | 字符串     | 连接 |

使用值： "7" 和 "3" 得到的结果是：

| 表达式 | 结果    |
|:------:|:-------:|
| 7 + 3  | 10      |
| 7 - 3  | 4       |
| 7 * 3  | 21      |
| 7 / 3  | 2.33333 |
| 7 % 3  | 1       |
| 7 3    | 73      |




# Reference

[1] [GNU awk guide](https://www.gnu.org/software/gawk/manual/gawk.html)
