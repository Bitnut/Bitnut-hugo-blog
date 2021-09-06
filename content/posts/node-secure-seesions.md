---
title: "如何在 nodejs 中使用正确地使用 session"
date: 2021-0907T08:04:50+08:00
categories:
  - backend
tags:
  - "express"
  - "nodejs"
  - "javascript"
---

## 前言

最近公司希望重构论坛项目，计划使用 nodebb 这个基于 nodejs 的论坛开源项目。

nodebb 这个项目有个不错的设计，它的一些非核心功能是可以使用插件化的方式进行拓展的。因此稍微研究一下就可以在不修改源代码的前提下，对 nodebb 进行个性化的定制。

一番折腾之后，论坛对接上了账户中心。但是在布署 nodebb 的时候却遇到了这样一个问题：

> 论坛布署起来之后，免密登录操作一直无法建立用户 session，导致前台循环请求登录最后 nginx 报错 `too many request`

## 分析和排查

前台的循环请求只是表象，我们需要找到问题的核心。正如前言里所说的： `用户无法和后台建立 session`，这暴露了这是一个与 session 有关的问题。


首先，在我本地开发和运行的代码是没有出现这个问题的。这说明需要将本地和线上的区别作为切入点。


其次，既然是和 session 相关的问题，那么基本可以确定问题出现在后端。前台可以反复发起请求，说明浏览器和服务器之间并不存在连接问题。


有了以上的前提，就有了大概的思路了。


经过一番排查，终于将问题定位到了布署的端口上。由于布署的时候，在配置文件中指定了 nodebb 使用 80 端口，导致其并没有起用 `trust_proxy`，核心代码如下所示：

``` javascript
port = parseInt(port, 10);
if ((port !== 80 && port !== 443) || nconf.get('trust_proxy') === true) {
	winston.info('Enabling \'trust proxy\'');
	app.enable('trust proxy');
}
```

可以见到，代码里判断了端口号是否是 80 或者 443 ，如果不是才起用 `trust proxy` 这个特性。


那么这个 `trust proxy` 到底是何方神圣？不使用它又为什么导致了用户无法在后台建立 session？


## 深入


其实这是一个关于建立安全 session 的问题。这个问题很简单，但是可能就是因为太简单了，导致 express/session 的[文档](https://github.com/expressjs/session)里也没有咋提。

>When running an Express app behind a reverse proxy, some of the Express APIs may return different values than expected. In order to adjust for this, the trust proxy application setting may be used to expose information provided by the reverse proxy in the Express APIs. The most common issue is express APIs that expose the client’s IP address may instead show an internal IP address of the reverse proxy.
>
> 原文里就是这么写的，大意就是当使用反向代理代理 express app 的时候，有得 express api 会提供和预期不一样的返回值。为了使用反向代理提供的一些值，`trust proxy` 这个玄想需要被打开。
>


在我的实践里，就属于这样一种情况。nodebb 项目被布署在容器里，nodebb 容器的请求都通过 nginx 容器进行代理。


这里简单介绍一下我的布署环境：

``` javascript
[EXPRESS APP] <- HTTP -> [NginX] <- HTTPS -> [PUBLIC INTERNET] <-> [CLIENT]
```

可以看到，nginx 和 express 服务器之间使用的是 http 协议，nginx 和客户端（浏览器）之间使用 https 通信。


而所谓的 `trust proxy` 信任的 `反向代理的值` 实际上就是 反向代理提供的几个广泛使用的 `http header`:

1. X-Forwarded-For
2. X-Forwarded-Host
3. X-Forwarded-Proto

这里不展开分析这三个头。但是需要注意的是，如果在 express 中启用了 trust proxy， 那么这三个字段都需要在反向代理中配置好。因为这几个 header 无论谁都可以修改和添加。错误的配置会导致安全问题。

## 最终解决

这个配置的问题我也是第一次遇到。花了我好长时间 debug :(，理清楚之后，其实可以知道不只是端口配置会导致这个现象。只要和反向代理相关的设置、或者代码中没有启用 `trust proxy`, 都有可能出问题。所以我总结了一下这里的套路，希望下面的代码可以帮助到你。

* express 部分

主要是开启 trust proxy。

``` javascript
app.enable('trust proxy');
app.use(express.bodyParser());
app.use(express.cookieParser());
app.use(express.session({
   secret: '超级复杂的密码',
   proxy: true,
   key: 'session.sid',
   cookie: {secure: true},
   store: new sessionStore()
}));
```

* nginx 部分

主要是配置好 X-Forwarded-* 部分。

``` nginx

server {
    listen       443;
    server_name  localhost;
    ssl                  on;
    ssl_certificate      /etc/nginx/nodeapp.crt;
    ssl_certificate_key  /etc/nginx/nodeapp.key;
    ssl_session_timeout  5m;
    ssl_protocols  SSLv2 SSLv3 TLSv1;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;

    location / {
        # 注意这里！
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_read_timeout 5m;
        proxy_connect_timeout 5m;
        proxy_pass http://nodeserver;
        proxy_redirect off;
    }
}
```
