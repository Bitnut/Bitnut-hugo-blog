---
title: "详细论述 CryptDB 的原理"
date: 2017-09-11T08:52:18+08:00
---

> 版权声明：本文为博主原创文章，欢迎转载；转载请注明来自 瓜哥

## 博主写的 CryptDB 另外几篇相关文章：

[cryptdb 安装及使用说明](https://bitnut.github.io/posts/cryptdb-install/)

[CryptDB 简单原理论述](https://bitnut.github.io/posts/cryptdb1/)

## 相关资料

想要理解CryptDB, 你可能需要阅读如下资料：
CryptDB. Popa, R. A., et al. (2011). CryptDB: protecting confidentiality with encrypted query processing. [文章链接](http://web.cs.ucdavis.edu/~franklin/ecs228/2013/popa_etal_sosp_2011.pdf)

Guidelines for Using the CryptDB System Securely [链接](https://eprint.iacr.org/2015/979)

* 其他可能有用的资源:

CryptDB 有软件的下载和使用介绍。

GitHub - CryptDB/cryptdb: A database system that can process SQL queries over encrypted data. 这个比较方便，建议看这个！

Raluca Ada Popa's Homepage 建议～


## 主要结构：

Database proxy和一个unmodifiedDBMS

User-defined functions (UDFs) [用来执行密码操作]

## 安全保障：

* 面对thread1：

将所有的SQL查询用proxy拦截。

利用proxy加解密从DBMS传过来的数据，也会转换一些操作（比如说计算方面的+会利用*代替）

* 面对thread2：

原理看得不是很懂，只知道是说对于不同的数据是会采用不同的数据进行加密的（比如说不同用户的加密秘钥是不同的）。

但是在这个情况下是能够保护到没有在数据库被攻击期间登录的用户的。而且仅仅是thread1下采取的措施是不足以保证thread2下的安全的，因为敌人可以得到密钥

## Thread1下的密码学实现：

几个密码算法的简要概括：

* RND：

最高级别的密码学保护，所有的计算都是不被允许的，甚至相同的两个明文映射到的都是不同的密文！（这里看图一感觉就不是很懂了！为什么会支持COUNT和UPDATE这样的操作？？大神请告诉我）

* DET

只会暴露那些相同的映射关系，所以可以执行=的断言操作。

理论上可以实现：

join，GroupBy, count, distinct, in, not in, =, !=

* OPE

可以在密文上实现排序！因为加密之后的密文仍然会暴露他们的大小关系。

理论上可以实现：

Orderby, min, max, sort

* HOM（同态加密）

可以实现在加密的密文上的计算，但是需要转换！原理：HOMk(x)*HOMk(y)=HOMk(x+y)通过吧加密值相乘可以求出和的加密值。同样计算average时返回count值和sum值即可！

理论上可以实现：

Sum,multiply, average

* JOIN

Join的设置是有必要的，因为DET的实现使用了不同的key以防止跨列相关联（作者的话=。=难以理解）。

* SEARCH

支持LIKE操作，利用了特殊的规则把内容分成块状，去重（重复的字块）之后打乱顺序再使用Song

* et al的算法加密，非常安全，几乎可以达到和RND一样的加密程度（因为DBMS只能通过UDF和传入的密文检查是否match到，其他的信息对于DBMS来说都是一抹黑）。

请结合下图了解几种加密算法之间差别，甚至同一个算法在不同的使用场景之下都会有不同的加密效果！

图一

实际的运用场景：

## 概念：可调节的基于查询的加密

解释：为了在提供数据库服务的同时保证最高的安全保障，cryptDB会调节加密的方式。

下面的洋葱图展示了在最原始的情况下，数据被加密的情况。每向外一层都代表了更高程度的加密。同样的基于性能的考虑，为不同的数据类型准备几种不同的洋葱丝有必要的。例如：对于string类型add操作是没有意义的，对于integer来说search操作也是毫无效果的。

图二

加密的秘钥是不同的，仅仅在同一个onion层和同一列下key是统一的。这样防止DBMS获取到更多别的关系下的信息。

解开加密层：

利用DECRYPT_***

UDF（***是某种加密算法）这个UDF是嵌入到查询语句之中的。例如：

UPDATE Table1 SET

C2-Ord= DECRYPT RND(K, C2-Ord, C2-IV)

解密操作只会发生在对于查询对数据加密要求贬低的时候，在要求不变的时候，解密操作不会发生。

## 两种join

Equi-join和range

join。

为了实现equi-join不同的列之间必须用同样的key进行加密，否则数据库实际上没有办法进行对比的操作。另外不能join到一起的列不能够使用同样的key（意思是因为某些原因，即使是可以join的，但是应用层不希望他们可以执行join操作）

在上面的讨论之后，如果事先知道了要join的列名equi-join是很容易实现的。但是问题是如果proxy代理并不知道呢（因为在DBMS内无论是表名还是列名都是隐藏的）？事情会变得比较复杂。

* JOIN-ADJ

为了解决列对（column pair）不是先验的情况下join操作的执行问题，CryptDB给出了JOIN-ADJ这种‘新的’密码学原型（其实不是很新就是了）。

* JOIN-ADJ是个啥？

论文里谈到JOIN-ADJ是一种【对输入确定性的函数】（就是相同的明文对应的一定是相同的JOIN-ADJ值），同时也是【可调整的利用秘钥加密之后的hash值】（意思是这个hash值由对应的加密算法生成，而且带有可调整的附加属性的）。

* JOIN-ADJ怎么使用？

首先JOIN-ADJ是不可置反的。

因此利用了JOIN-ADJ的JOIN加密方案由如下所示：

JOIN(v) = JOIN-ADJ(v)||DET(v)…….（这里的||符号起到连接字符串的作用）

在这个结构之下，CryptDB通过比较JOIN-ADJ(v)来实现比较属性名是否相同，通过解密DET(v)来获取被加密的属性值。

当每次proxy悉知了有心得JOIN操作到来，它会向DBMS传递一个key使得DBMS可以调整两个列之间的JOIN-ADJ值，使得他们的值趋于一致（就是变成一样的）（当然必须明文要是相同的才行）。一旦他们经过了调整，之后对于这两个列的JOIN操作就无需再次调整他们的JOIN-ADJ值了，因为他们会将这个值保留，直到需要作出改变为止。

显然，这里提到的JOIN操作是具有可传递性的，AB、AC可以实现JOIN，那么BC同样可以。这样ABC形成了一个所谓的“可传递组”的概念（看起来其实就是属性闭包嘛）。

注意他这里为什么要用“可传递组”这样一个奇葩的名称，实际上作者的对于可以实现JOIN操作的列对的管理是通过这样的标记来实现的：（表名+列名），并不是普通意义上的属性闭包。根据作者的说法，这样的标记是出于【避免波动】（不是很懂）这样的考虑，而且是为了让所有在“可传递组”中的列能共享同样的join-base。

CryptDB通过选择字母表顺序上的第一个标签作为join-base，因此如果每次有新表加入，而且这个表的标签会成为新的join-base的话，n个列的情况下，总共需要n(n-1)/2次join转换（即对JOIN-ADJ值的调整）

* JOIN-ADJ的生成
这里没有难度，请自己看生成方式。

威胁2下的CryptDB：

第四章在一个论坛网站phpBB的模型上讨论了这个问题如何在数据库陷入险境的时候，减轻收到的数据损失？

CryptDB作出了以下两种保证来处理威胁2。

1.私人之间传递的信息是对他人隔离的。

2.某个论坛中发表的言论信息只有属于该论坛的人才能看见。

这样的保证有两个挑战：

1.在SQL查询的层次就需要获取对于共享信息的权限控制策略。（因此要在建表的时候声明principles）【4.1】

2.减少敌方能够获取的信息量（key chaining）【4.2】

## 策略的注解：

软件开发人员通过对CryptDB中数据库模式结构的定义，来指定在SQL查询这个层次上的策略。

如何进行注解？

开发人员会指定每一个数据实例其对应的principle是什么。Principle是一个实体，例如一个用户或者一个小组，利用他们可以很好的对权限进行定义。

CryptDB使用的是自己定义的principal而不是现成的DBMS的principles。因为现成的principle提供的定义细粒度不够，不足以满足开发的需求。其二CryptDB在principle之间需要实现显式的特权授予（SPECK_FOR），这也是现有的DBMS principle不能够提供的！

注：特权的意思其实就是能够对数据进行解密操作。

注解需要以下3步：

请对照下面的定义来理解！

Step1：

定义principle types：利用PRINCTYPE关键字定义。每一个principle都是某个principle type的实例（就类似C++里面变量和变量类型的概念）

一共有两种principle：external和internal（注意是除了external就是internal了下面不再重复这个概念）。

External：这种principle代表了那些端用户（end user），这些端用户通过显式的使用密码得到自己的权利。

Internal：这种principle的特权是可以被external的principle获取的，但是只能通过显式的声明来实现授予。

Step2：

显式的定义每一列是否包含有敏感的信息，同时也定义哪些principle拥有获取这些敏感信息的权利。使用ENC_FOR来实现这样的操作。如下图中的表privmsgs中，只有principle 5可以解密msgtext x37a21f。

Step3：

可以利用SPEAK_FOR定义授权的规则。语法：(a x) SPEAKS_FOR (b y)

直译过来这个声明的意思就是x类a principle声明了自己拥有y类b

principle的特权。其实对着表来看的话就是a这个属性获取了b这个属性的特权。

注意：b这个属性只能待在声明所在的这张表里面，但是a就不一样了，a可以待在别的表里面。



## Key Chaining

其实没有说前面说的“属性”用来代替principle其实是不准确的说法。到了Key Chaining这里是绝对不可以用的。

每一个principle实例其实都关联了一个随机选择的key。假如某个principle A给另外一个principle B授权了，就是B speaks for A，那么A的key将会用B的key加密然后存储到数据库中的access_keys表格中。

敏感区域的数据是用和前面onion同样的方式加密的，区别在于这里是每一个实例都会被单独加密，而不是整一列使用同样的秘钥加密。

每个principle实例的key是由对称秘钥和公钥私钥对组成的。公钥加密开销比较大，因此普通情况下principle都是用对称密钥加密的。可是也有特殊情况，一旦涉及到有的principle没有登录，这时候别的principle都是没有办法获知没有登录的principle的对称密钥的，所以利用公钥加密的方法，把明文用公钥加密传给对方，等到对方上线的时候使用私钥解密即可。

CryptDB为每一个external principle派发一个随机key，这个key被存放在external_keys表格里面

每当地方想要通过更改SPEAKS_FOR来窥探隐私信息的时候，CryptDB的机制告诉我们，需要对access_keys表格进行相应的更改，想要更改这个表格必须获得被授权principle的key（前面讲过的），这就意味着只有这个principle登录的时候，敌方才能获取相应的key从而对这个principle的私密信息进行窥探。
