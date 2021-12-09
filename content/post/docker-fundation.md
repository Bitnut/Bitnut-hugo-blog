---
title: "为什么/如何使用 Docker？"
date: 2021-02-23T07:59:04+08:00
draft: true
categories:
  - 专题
tags:
  - "docker"
  - "linux"
---

# Docker 需要解决的难题

现在当我们提到布署、容器和虚拟化技术，我们首先想到的就是 Docker。 毫无疑问，Docker 已经成为了我们在开发过程中不可缺少的一部分了。在本文中，我更希望抛开底层的技术细节，从宏观的、简单的角度对 Docker 进行分析，并在分析中得出一些软件设计和产品设计的灵感。

## 在 Docker 出现之前，我们在软件开发过程中到底遇到了什么难题？

* 运行时的维护

在相对简单的情形下（例如开发、测试、和线上布署都使用相对较少的 server），服务器的维护和代码升级并不会给我们带来太大的困扰，我们甚至可以手动完成这些维护和升级（包括`系统、系统组件和依赖、配置`等），就像是在自己的开发环境下一样。

但是在可以想见的未来，一旦 server 的数量大量增加，如公司的业务进行了拓展、用户出现指数型增长等等，手动的完成这些维护工作将会是一种开发过程中的负担。

* debug 需求

当我们在布署的时候采用了负载均衡这一类方案的时候，用户可能会访问到出错的 server，这个时候用户可能会试图去刷新页面，反向代理会把用户的请求代理到另一台服务器上。这个过程并没有太大毛病。

    问题在于我们如何定位到问题？如果说这是一个配置问题，而且我们的服务器集群中使用了超过 100 台 server，这个时候一个一个去对比具体配置并试图定位问题将会是一场灾难。