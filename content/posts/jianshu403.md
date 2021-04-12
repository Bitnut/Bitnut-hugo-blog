---
title: "解决 http 访问简书图片资源 403 问题"
date: 2021-04-11T09:44:50+08:00
tags:
  - Protocal
  - http
  - html5
---

## 问题起因

最近遇到一个问题，我有些文章放在简书还没有同步过来个人博客上。可就是当我移植过来之后，突然发现图片都显示不出来了。

具体情况如图：

![报错信息](/static/jianshu-403.png)

## 识别问题

经过搜索，发现 403 报错的原因是服务器拒绝了我们的请求。说明我们可能哪儿做错了。

于是我直接在浏览器中发起请求，发现是可以正常拿到资源的。诶？？？ 这是怎么肥事？？

再经过一番研究，终于找到了罪魁祸首所在！如图，在我博客发起请求的请求头中，我的博客页面乖乖的把一个叫做 `referrer` 的字段给填上了。

![referrer 字段](/static/jianshu-gotcha.png)

说下解决方法： 在 html 代码上加上 meta 标签：  <meta name="referrer" content="no-referrer" /> 即可。

比如我在自己的模版文件里：

![添加配置](/static/baseof-meta-config.png)

## http 请求头中的 referer

http 请求头中有一个 `referer` 字段，其作用是表示发起 http 请求的源地址信息。

这是个可选的请求头，说明我们可以不传这个参数，事实上出于隐私考量，也有很多浏览器并不默认传递这个参数，如果浏览器选择传递了这个参数，那么它的值就不会改变了，不能定制referrer里面的值。

这是一个对服务端有利的参数，因为服务端在拿到这个值后就可以进行相关的处理。例如简书的图片资源，可以通过 referer 值判断请求是否来自简书本站，若不是则返回 403 或者重定向返回其他信息，从而实现了图片的防盗链。

[html5](https://html.spec.whatwg.org/multipage/links.html#link-type-noreferrer) 支持了所谓的 `noreferrer` 参数，从而我们可以通过在 meta 标签中设置 `noreferrer` 达到发送请求却不会带上 referer 参数的效果，对方服务器也就无法拦截了。

### 一个小问题

为啥一会 `referer` 一会又 `referrer` 的？ 这两个是一个东西吗？？

是的，写错了 ：P，见 [rfc 2616 规范](https://tools.ietf.org/html/rfc2616#section-14.36)

## 服务端到底做了啥配置？？


主要是 nginx 防止盗链：


在服务器如何配置 nginx 达到这样的效果呢？

在 nginx 的配置文件: nginx.conf 中的 server 段中添加如下代码:

``` nginx
location ~* \.(gif|jpg|png|jpeg)$ {
        valid_referers none  valid.url.com;
        if ($invalid_referer) {
                return 403;
        }
}
```

第一行以文件格式后缀匹配图片资源路径，然后通过 valid_referers 添加合法的 referer 地址，加上 none，表示没有传 referer 也是合法的，最后 referer 不合法的情况返回 403。


## 参考资料

* [RFC 2616](https://tools.ietf.org/html/rfc2616#section-14.36)
* [wikipedia](https://en.wikipedia.org/wiki/HTTP_referer)
* [HTML Living Standard](https://html.spec.whatwg.org/multipage/links.html#link-type-noreferrer)
