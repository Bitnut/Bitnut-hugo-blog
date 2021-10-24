---
title: "Linux Awk 入门（02）"
date: 2021-05-12T13:00:28+08:00
categories:
  - 专题
  - 教程
tags:
  - "GNU"
  - "Linux"
  - "Awk"
---

![gun-awk](/static/linux-awk/gnu-awk.png)

## 前言

根据上文，我们了解到了 awk 的基本特性和书写方式。这一章，我们开始深入 awk 编程。

仔细思考 awk 的核心思路我们可以发现，我们在 awk 中聚焦的核心是 `记录` 以及 `处理记录`，而针对记录，如果我们可以筛选出我们需要的记录，那么无论是程序的执行次数还是编程复杂度都会大大降低。

那么，如何使用 awk 筛选出合适的记录呢？

## 一、 基础语法

首先，让我们来掌握一些基本的工具。

### 1. 基本运算符

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

### 2. 与或非

和大多数语言一样， awk 中使用 `&&` `||` `!` 分别表示 `与` `或` `非`。

### 3. 正则匹配

| 运算符 | 涵义   |
| ~      | 匹配   |
| !~     | 不匹配 |

awk 支持 [extended regular expressions](https://www.gnu.org/software/grep/manual/html_node/Basic-vs-Extended.html)，且表达式必需被下划线: `//` 包裹。

案例如下：

``` awk
foo !~ /Hello/
bar ~ /(one|two|three)/
```

### 3. 内置变量

其实到这里我们已经可以进入到实战中了，不希望花太多时间的话，现在就可以跳到下一章开始实战。

| 变量     | 涵义                                                                            |
|:--------:|:-------------------------------------------------------------------------------:|
| $0       | 当前记录（这个变量中存放着整个行的内容）                                        |
| $1~$n    | 当前记录的第 n 个字段，字段间由 FS 分隔                                         |
| FS       | 输入字段分隔符 默认是空格或 Tab                                                 |
| NF       | 当前记录中的字段个数，就是有多少列                                              |
| NR       | 已经读出的记录数，就是行号，从 1 开始，如果有多个文件话，这个值也是不断累加中。 |
| FNR      | 当前记录数，与 NR 不同的是，这个值会是各个文件自己的行号                        |
| RS       | 输入的记录分隔符， 默认为换行符                                                 |
| OFS      | 输出字段分隔符， 默认也是空格                                                   |
| ORS      | 输出的记录分隔符，默认为换行符                                                  |
| FILENAME | 当前输入文件的名字                                                              |

## 二、实战案例

ok 我们已经了解到匹配模式的最基本工具了，运算符 & 正则 & 内置变量构成模式，比较运算符规定匹配方式。我们就得到了 n 多匹配的 `规则`，也就是前文提到的 `pattern`。

下面开始实战。

首先，把一些打印放到文件里。

我选择了分析 `netstat` 这个命令输出的一部分：

### 1. 初步过滤记录

我们仅希望得到有关当前系统中`网络链接`的具体信息。

`$ netstat | awk '$1!~/Active|unix/ && NF!=7' > netstat`

我的输出如下：

``` shell
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 193.9.44.77:57380       142.250.101.188:5228    ESTABLISHED
tcp        0      0 193.9.44.77:https       113.66.33.4:57869       ESTABLISHED
tcp        0      0 193.9.44.77:37290       152.199.4.33:https      ESTABLISHED
tcp        0      0 193.9.44.77:55242       40.91.80.66:https       TIME_WAIT
tcp        0      0 193.9.44.77:https       113.66.33.4:54114       ESTABLISHED
tcp        0      0 193.9.44.77:48186       142.250.101.188:https   ESTABLISHED
tcp        0      0 193.9.44.77:ssh         113.66.33.4:58356       ESTABLISHED
tcp        0      0 193.9.44.77:https       113.66.33.4:57313       TIME_WAIT
tcp        0      0 193.9.44.77:37552       server-52-85-193-:https ESTABLISHED
tcp        0      0 193.9.44.77:https       113.66.33.4:54168       ESTABLISHED
tcp        0      0 193.9.44.77:38470       42.186.18.104:http      TIME_WAIT
tcp        0      0 193.9.44.77:https       113.66.33.4:40763       TIME_WAIT
tcp        0      0 193.9.44.77:39704       lb-140-82-114-25-:https ESTABLISHED
tcp        0     48 193.9.44.77:ssh         113.66.33.4:58242       ESTABLISHED
tcp        0      0 193.9.44.77:44800       ec2-18-208-101-52:https ESTABLISHED
tcp        0      0 193.9.44.77:https       113.66.33.4:54133       ESTABLISHED
tcp        0      0 193.9.44.77:43774       74.125.137.188:5228     FIN_WAIT2
tcp        0      0 193.9.44.77:https       113.66.33.4:56067       ESTABLISHED
tcp        0      0 193.9.44.77:https       113.66.33.4:9964        ESTABLISHED
tcp        0      0 193.9.44.77:53038       52.167.249.196:https    TIME_WAIT
tcp        0      0 193.9.44.77:https       113.66.33.4:54068       TIME_WAIT
```

其中，`$1!~/Active|unix/` 使用了简单的正则匹配，过滤了非网络链接的信息以及一些提示信息

`NF!=7  {print $0}'` 使用了内置变量 `NF` 代表每行的记录数，过滤了超过七列的记录。

`print $0` 打印了一整行记录。其实在我们的这个情形中，也可以不用这个打印语句，达到同样的效果。

### 2. 获取真正需要的记录

* 例:

获取 `State` 是 `TIME_WAIT` 的 使用 `https` 的记录的第 2/3/4/5 列：

`awk '$6=="TIME_WAIT" && $5~/.*:https/ {printf "%-8s %-8s %-18s %-22s\n",$2,$3,$4,$5}' netstat`

得到打印结果：

``` shell
0        0        193.9.44.77:55242  40.91.80.66:https
0        0        193.9.44.77:53038  52.167.249.196:https
```

### 拓展资料

其实到这里，我们可以说对 awk 已经初步入门了。对上面提到的一些技术细节感兴趣，或者需要的朋友可以到 gawk 的文档看看。

例如：

* [内建变量](https://www.gnu.org/software/gawk/manual/gawk.html#Built_002din-Variables)
* [正则](https://www.gnu.org/software/gawk/manual/gawk.html#Regexp)

# 参考资料

[1] [GNU awk guide](https://www.gnu.org/software/gawk/manual/gawk.html)

[2] [awk tutorial](https://www.grymoire.com/Unix/Awk.html#toc_Awk)
