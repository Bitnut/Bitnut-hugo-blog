---
title: "CryptDB  简单原理论述"
date: 2017-09-11T08:52:53+08:00
tags:
  - "cryptdb"
  - "database"
---

## 博主写的 CryptDB 另外几篇相关文章：

[cryptdb 安装及使用说明](https://bitnut.github.io/posts/cryptdb-install/)

[详细论述 CryptDB 的原理](https://bitnut.github.io/posts/cryptdb2/)

## 1.面对threat 1

* 解决方案：通过把对数据库进行的操作（选择，连接，投影等操作）进行特殊处理，使得这些操作能够执行在已加密的数据上

这种解决方案存在的一大问题就是，目前还没有研究出一种加密方式，使得任何执行在数据库上的操作，都能正确运行在加密过的数据上。因此就意味着需要在数据库服务端存放多种经过不同方式加密的数据。当用户执行特定操作时，Mysql-proxy就会截获操作请求并使用使用特定的加密方式对该操作进行加密，使得该操作能够正确运行在Server端。

以下是proxy采用的几种加密算法

### 1. Random (RND)

RND是最安全的一种加密方法，即使是两个相同的明文，也可以映射到不同的密文上面。这种方法可以用来抵抗自适应选择明文攻击，但是缺点就是很难在密文上进行操作。实现方法有AES等，这里不详说。

### 2. Deterministic (DET)

DET的安全性会相对弱一点，相同的明文映射到相同的密文中，所以可以很方便地对密文进行操作，例如相同值查询等，但是对于不同的表，相同的列，使用的 key 不一定相同。

### 3. Order-preserving encryption (OPE).

OPE允许数据的顺序查找，它保证当x

### 4. Homomorphic encryption (HOM)

HOM类型的加密可以使得一些计算可以直接在加密后的数据上面，例如HOMK(x)·HOMK(y) = HOMK(x + y)，这类算法可以保证一些数学运算例如SUM等操作。

### 5. Join ( JOIN  and OPE- JOIN )

这种加密可以支持 join 运算，包括范围 join 以及等值 join （自然连接）。因为在DET中，相同的列，在不同的表中可以用不同的 key 来加密，所以相同的值在不同的表加密后的值不一样。所以此类算法就是为了使加密后的列表能正确地进行连接。

#### JOIN-ADJ

 JOIN-ADJ  是一种【对输入确定性的函数】（就是相同的明文对应的一定是相同的 JOIN-ADJ 值），同时也是【可调整的利用秘钥加密之后的hash值】（意思是这个hash值由对应的加密算法生成，而且带有可调整的附加属性的）。

##### JOIN-ADJ 怎么使用？

首先 JOIN-ADJ 是不可置反的。

因此利用了 JOIN-ADJ 的 JOIN 加密方案由如下所示：

 JOIN (v) = JOIN-ADJ (v) ||  DET(v) （这里的||符号起到连接字符串的作用）

在这个结构之下，CryptDB 通过比较 JOIN-ADJ (v)来实现比较属性名是否相同，通过解密 DET(v) 来获取被加密的属性值。

当每次proxy悉知了有新的 JOIN 操作到来，它会向 DBMS 传递一个 key 使得 DBMS 可以调整两个列之间的 JOIN-ADJ 值，使得他们的值趋于一致（就是变成一样的）（当然必须明文要是相同的才行）。一旦他们经过了调整，之后对于这两个列的 JOIN 操作就无需再次调整他们的 JOIN-ADJ 值了，因为他们会将这个值保留，直到需要作出改变为止。

显然，这里提到的 JOIN 操作是具有可传递性的，AB、AC可以实现 JOIN ，那么BC同样可以。这样 ABC 形成了一个所谓的“可传递组”的概念。

作者对于可以实现 JOIN 操作的列对的管理是这样实现的：（表名+列名），使得所有在“可传递组”中的列能共享同样的 join-base。

CryptDB 通过选择两列中字母表顺序第一的列来作为基准，是为了防止发散，通过多次 join 之后，最后可以达到一个稳定的，有共同 join-base的状态，这样就形成一个 transition 组，比较像算法中的迭代法。

### 6. Word search (SEARCH)

这类加密方法允许对密文进行像sql语句中的like语句之类的操作。但是cryptdb只支持一些完整的词汇匹配，不支持任意的相似匹配。Server可以知道是否有信息可以匹配到一个token，但是它无法得知这个token的内容以及匹配到的信息的明文。

通过上述几种加密算法，明文数据在服务器端被加密保存成对应密文数据。当接收到proxy加密后的操作请求，就会针对对应操作处理并返回密文数据。

但是这种通过将几种加密方式加密后的密文全部保存在客户端的做法存在的一大问题是，使用多种加密方式在一定程度暴露其关键信息，使得安全性降低。而为了解决这个问题，CryptDB 采用了adjustable-query-based encryption的可调整的基于查询的方式，当需要对某一列相同值进行查询时，就对这一列进行DET加密。而当需要执行某种操作时，就会在proxy端使用相应类型的加密算法后发送给服务端。

以上的实现需要在提前知道用户所需要执行的所有操作的情况下才能被完全满足。

但是实际上是很难提前知道你所要做的所有操作。

这时候就需要洋葱模型，把数据包成一个洋葱，如下图，越外层的加密越安全，而越靠近明文的层提供的功能就越多。所以在一开始，先通过最外层的加密类型，把明文加密成密文保存在客户端，当要用到相应操作的时候，如果当前的所拥有的密文无法满足其操作，就通过一系列算法把洋葱剥开，提供相应的密文进行操作，当下一次要进行同样操作的时候，这时候不需要剥洋葱，只需要用上次的密文进行操作即可。

对于每一个洋葱，同一列的明文数据通过同一个 key 进行加密，但是对于不同表、不同列使用不同的 key 进行加密。

（如图，ID列和Name列各自分成了4个洋葱）

CryptDB 使用自定义函数进行层的解密，如果要只想相同值查找的时候，可以使用名为DECRYPT_RND的自定义函数进行解密：

解开一层之后，以后对这一列进行相同值查找的时候，就可以直接使用这个密文进行操作。

## 2.面对threat 2

解决威胁二

当proxy和application端也会受到攻击的时候，需要保证泄露的数据尽可能少。

CryptDB 提供了以下机制来解决威胁二：

首先是要向共享数据提供访问控制策略。CryptDB 通过ENC_FOR以及SPEAK_FOR语句提供注释的功能，实现了对共享数据的访问权限进行限制。这一过程形成的 key chain保证了当系统受到攻击的时候，只有登陆中的用户的数据会被泄露，未登陆的用户的数据不会被泄露。

具体实现方式

CryptDB 首先需要对主体(principal)的类型进行定义，例如用户，用户组等等。有两种主体，一种是external principal，另一种是internal principal。

对于external

principal，需要提供密码给proxy，才能得到相应principal的权限。而internal principal获得权限需要在系统中对其进行授权。

CryptDB 使用的是自己定义的principal而不是现成的 DBMS 的principles。因为现成的principle提供的定义细粒度不够，不足以满足开发的需求。其二CryptDB 在principle之间需要实现显式的特权授予（SPECK_FOR），这也是现有的 DBMS  principle不能够提供的！

系统为每个principal都随机生成一个 key ，并且把 key 用相应用户的密码加密后，放在external_ key s表里面，这样当用户之类的改变密码的时候，私密数据的 key 可以不用改变，只需要在external_ key s表中对相应的principal的 key 用用户的新密码进行重新加密。

当external主体登陆进系统之后，系统会把该用户名以及密码放在一个表cryptdb_active中，密码用于解密相应主体的 key 以读取相关数据。Proxy会把这个表读进内存里面，server端不会得到这个表，并且proxy拦截一切对该表的访问。如果用户没有登陆，就不会出现在此表中，意味着即使proxy受到攻击，表里面的内容泄露了，未登录用户的数据大部分（可能会出现处于同一个principal的人登陆之后泄露数据的情况）不会被泄露，因为密码不在表上面，这样保证了未登录用户的数据。

但是有一些特例，例如管理员之类的可访问数据比较大（权限很高），很容易会造成大量数据的泄露，所以管理员账号不能随便记录在cryptdb_active表里面，如果管理员要以普通身份登陆的时候，就需要另外分离出一个有相应权限限制的账号，写入cryptdb_active表中，这样可以在大部分时间里防止大量数据的泄露。

CryptDB 使用两种语句对敏感数据进行注释：

（1）在表格中对表项用ENC_FOR语句对数据进行注释实现对数据的访问控制。A ENC_FOR B（A是数据，B是主体），意味着只有主体B可以访问数据A，ENC_FOR的实现原理是用主体B的 key 对数据A进行加密。

（2）利用SPEAK_FOR语句对表格进行注释，给一个主体授予另一个主体的权限。这种做法的格式为(a x) SEPAK_FOR (b y),其中x和y为主体类型，a和b是主体。意思是x类型的主体a有y类型的主体b的权限，b能访问的内容，a也有权限访问。

SPEAK_FOR的实现原理是用主体a的 key 对主体b的 key 进行加密，当需要访问相应数据的时候，可以通过主体a的 key 对主体b的 key 进行解密，然后通过主体b的 key 对相应数据进行访问。当主体A通过SPEAK_FOR语句与主体B相连的时候，主体B的 key 会用主体A的 key 加密，然后放在一个特殊的表access_ key 表上面。但是数据的加密在共享数据双方都在线的情况下采用对称密钥（加密和解密的密钥相同）进行加密，而当某一个主体没有登陆进系统的时候，无法得到该主体的symmetric  key ，所以这个时候会采用公钥加密数据并保存，当主体再次登录的时候使用自己的私钥进行解密，获得数据。多个SPEAK_FOR就形成一条 key  chain，要找某个数据的时候，只需要顺着 key  chain，从用户密码，一直找到要找的数据为止。

每当敌方想要通过更改SPEAKS_FOR来窥探隐私信息的时候，CryptDB 的机制告诉我们，需要对access_ key s表格进行相应的更改，想要更改这个表格必须获得被授权principle的 key ，这就意味着只有这个principle登录的时候，敌方才能获取相应的 key 从而对这个principle的私密信息进行窥探。

## 五、参考文献列表

1. 极简密码学,by曾兵

2. CryptDB .Popa, R. A., et al. (2011). CryptDB : protecting confidentiality withencrypted query processing. Proceedings of the Twenty-Third ACM Symposium onOperating Systems Principles. Cascais, Portugal, ACM: 85-100

3. [Guidelines for Using the CryptDB  SystemSecurely](https://eprint.iacr.org/2015/979)

4. [CryptDB 项目主页:](http://css.csail.mit.edu/cryptdb/)

5. [CryptDB 发明人主页:](https://people.eecs.berkeley.edu/~raluca/)
