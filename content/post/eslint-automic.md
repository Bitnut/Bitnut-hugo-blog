---
title: "在 JS/TS 开发中快乐地使用 eslint"
date: 2021-12-08T3:00:55+08:00
categories:
  - programming language
tags:
  - "nodejs"
  - "javascript"
  - "typescript"
  - "eslint"
---

## 前言

在 JS/TS 开发中，往往绕不开 eslint。在多人合作开发的时候，使用 eslint 这个静态代码检查工具，可以很好的帮助我们构建风格一致的代码仓库，同时检查出隐藏的代码问题，帮助我们减少 代码出现低级错误的可能。

eslint 固然是一个非常快乐的，但是在如果在编程的过程中，eslint 不断地尝试 `教我们做事`，输出大量编程无关的信息，这能忍？ 我们应该如何快乐的使用 eslint，又享受到它带来的好处呢？

## 有关 lint

eslint 是一种 linter，只是在 js 的世界里，它的名字叫做 eslint。先来了解一下 linter 的起源和作用对我们使用 eslint 很有帮助。


* 起源

在计算机编程领域，[lint](https://en.wikipedia.org/wiki/Lint_(software)) 的概念由来已久。早在 1978 年，贝尔实验室的科学家为了调试 c 语言程序在 unix 机器上开发了他命名为 lint 的小工具，用来检查代码中可能发生的错误。lint 在英文中的意思是衣物上由纤维堆积起来可以看见的球或者条。就像 lint 一样，代码中有些可能无关紧要的错误会影响到代码整体的整洁性，甚至会让代码运行之后产生不可预知的 bug。

* 作用

之所以会有 lint 工具的出现，无非是代码不够好。但是好的代码到底应该如何定义？对于人来说，整洁可读，没有运行时的 bug，精彩的算法，正确的分层结构这些都是好的代码应该具备的条件；对于机器来说，尽量短小的代码，最好是机器码，经过编译优化器优化的代码是好的代码。可见不同层面上的 `好` 代码不是一样的概念。


那么对于 linter 来说，什么样的代码是好的代码？ linter 一般会发现代码中存在的什么问题呢？


一般来说，linter 发现的代码错误可以简单分为两类：


一类是单纯的代码风格问题，如： 使用空格还是缩进/空格到底空几格（老问题了）；函数名和参数列表之间空不空格；if else 是否换行等等。


另一类则是代码中的不当用法，如： 声明从不使用的变量；js 中不处理异步的函数；js 中使用 var 而不是 let 等等。


前者只是简单的风格问题，而后者则是有可能引起极难调试的诡异 bug，导致我们浪费大量时间 debug 的错误实践。


## eslint 使用指北

1. 关掉编辑器安装的 eslint 插件

2. 在项目中添加如下脚本

脚本的大概运行步骤如下：

1. 在脚本中通过调用 git 查找到当前更改和添加的文件（注意这些文件必须被 git track 到，新文件没有添加进暂存区的话会找不到）。
2. 然后调用项目中的安装的 eslint 检查代码风格并自动修复简单问题。
3. 如果发现无法自动修复的问题，则将报错信息输出至 eslintReport.log 中。
4. 最后输出统计信息，帮助我们发现自己经常犯错的地方。


``` shell
#!/bin/sh
# requirement npx/eslint/git
# target JS/TS files
# only modified files tracked by git

for i in "$@"; do
    case $i in
        -c=*|--config=*)
            ESLINT_CONF="${i#*=}"
            shift # past argument=value
            ;;
        -i=*|--ignore=*)
            ESLINT_IGNORE="${i#*=}"
            shift # past argument=value
            ;;
        -h|--help)
            echo "-c=/--config= use eslint config file\n-i=/--ignore= use eslint ignore file"
            exitFlag=1
            shift # past argument=value
            ;;
        *)
            # unknown option
            ;;
    esac
done

if [[ "$exitFlag" -eq 1 ]]; then

    exit 0;
fi

if [[  -z "$ESLINT_CONF" ]]; then

    ESLINT_CONF=".eslintrc"
fi

if [[ -z "$ESLINT_IGNORE" ]]; then

    ESLINT_IGNORE=".eslintignore"
fi

LOG_FILE="$(basename "$0" | cut -f 1 -d '.').log"

echo "./$LOG_FILE"

npx eslint -c ${ESLINT_CONF} --ignore-path ${ESLINT_IGNORE} $( git status --porcelain | awk '$1!="D" {if ($1=="R") { print $4 } else { print $2 }}' | grep -E '(.ts|.js)$' ) --fix

if [ $? -ne 0 ]; then
    echo -e "ERROR: Please check eslint hints.\n"
    npx eslint -c ${ESLINT_CONF} --ignore-path ${ESLINT_IGNORE}  $( git status --porcelain | awk '$1!="D" {if ($1=="R") { print $4 } else { print $2 }}' | grep -E '(.ts|.js)$' ) -f unix | head -n -2  | awk '{print $NF}' >> "./$LOG_FILE";
    echo -e "Hightest eslint error/warning\n";
    echo -e "times\terror"
    cat "./$LOG_FILE" | sort | uniq -c | sort -rn | head -n 10
    exit 1
fi

echo "Perfect coding format!"
```

3. 在自己的 build 命令中添加运行上述脚本
