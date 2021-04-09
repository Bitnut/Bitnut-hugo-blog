---
title: "如何写出无法维护的 TS 代码（挫败 Microsoft 试图接管 javascript 的阴谋）"
date: 2021-03-20T19:11:51+08:00
tags:
  - "node"
  - "Linux"
  - "MS"
  - "ts"
---

# 如何写出无法维护的 TS 代码（挫败 Microsoft 试图接管 javascript 的阴谋）

## 前言

这篇文章主要来介绍一些毁掉你 ts 代码的方法。(doge

## 正文

>If builders built buildings the way programmers write programs, then
> the first woodpecker that came along would destroy civilization.
>~ Gerald Weinberg (born: 1933-10-27 age: 77) Weinberg’s Second Law

## TS 特性

* 不封装代码： 调用者需要知道被调用的所有的细节。
* 接口包装，包装，包装： TS 的 Utility Types 和继承特性让我快乐起来，尽量多包装几层接口，到时候好用，最好可以把高级特性多用几遍。
* 随版本和提交变动我的接口： 比如说，把 showTable(col: number, row: number): Promise<void> 改成  showTable(row: string, col: string): void，等 release 了几个版本后，再把其改回去。这样维护我写的程序的程序员们将不能很快地明白哪一个是对的。
* 膨胀 ENUM 类型： ENUM 把字符串类型都放某个 ENUM 里，方便复用。
* TS 的 namespace 特性是美妙的东西（比 ES6 的好理解，反正就是高级），在接口定义的地方大量使用 namespace。
* 避免过度使用接口。BS 接口，面向接口编程加重了我的心志负担，不用。
* 使用包装类型： String 和 string 哪个牛逼？
* 重载签名把条件更宽松的放在前面： 例： 同样传递一个参数，把使用 any 的放在最开头。
* 只因为参数不同就声名重载函数。
* 避免过度使用类： 类写起来不舒服（太长。花大力气弄成函数式的。
* 使用 Object.keys(): 我就要在 ts 里用这个方法，它太好用了。见[link](https://github.com/microsoft/TypeScript/pull/12253)
* 遇到难题就写断言： 类型判断老是过不了，又不能写 any，写非空断言好了。
* 使用缺乏设计的接口： 遇到难题又不能写断言，那随便定义个 interface 就好了。
* TS 的类型检查是老中医神药： ts 不是有类型检查么，很好，我不做测试也可以保证结果是对的。
* 钻研/利用 TS 的结构化类型定义的漏洞： 本来函数需要一个 dog 类型的参数，我就是想传个 animal 类型的。给 dog 类型的变量赋值 cat 类型的实例。
* export 函数不写返回类型和参数类型： 自己看定义。

## js 相关

* 使用 Array 构造函数初始化数组： 当我使用 Array(1, 2, 3)， Array(5) 这样的函数可以很好的糊弄新手，因为他们不知道这个坑，这会对他们的成长颇有助益。
* 不使用 eslint 检查代码问题： eslint 只会限制我的想像力（确信。
* 使用 for-in 语法: 遍历对象只好用 for-in 了。
* 花哨的符合 eslint 命名规则的命名： function capiTaliSaTion() {}。

## 编程习惯1：

* 偶尔故意写错函数名： 比如同时定义两个函数 prepare()、preparae()。
* 从不验证： 从不验证输入的数据，从不验证函数的返回值。因为 TS 都帮我们做好了，这样做是充分信任 MS 的表现。
* 把变量改在名字上： 例如，把setMargin(margin: int)改成，setLeftMargin(bla), setRightMargin(bla), setCenterMargin(bla)。
* 使用环境变量初始化： 如果你的代码需要使用环境变量。那么应该把类的成员的初始化使用环境变量，而不是构造函数。
* 尽善尽美的类： 我的类应该尽可能地拥有各种方法。不需要定义多几个类，那样会使得代码太过混乱。
* 面向对象是写出无法维护代码的天赐之物！ 如果你有一个类有十个类成员（变量和方法）。考虑一下简化类吧。比如写 10 个层次的继承，然后把这十个属性分别放在这十个类里面。如果可能的话，把这十个类分别放在十个不同的文件中。
* 使用错误的换行符： 同时使用 CRLF 和 LF，而且换来换去。看我提交记录的人会像侦探一样看完我的代码。
* 在注释中撒谎： 其实可以不用撒谎，只需要在改代码的时候不更新注释就好了。
