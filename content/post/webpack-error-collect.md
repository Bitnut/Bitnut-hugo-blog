---
title: "webpack 配置错误拾遗"
date: 2021-01-14T22:20:01+08:00
tags:
  - "web"
  - "linux"
  - "frontend"
---

## 1.  `...` 展开运算符错误

错误原因：

早期的 babel 有许多问题，一些早期的版本（可能现在也没有解决）无法识别的语法、运算符可以通过插件的形式给 babel 打补丁，这里就是其中一种。

Just install babel-plugin-transform-object-rest-spread module.

https://www.npmjs.com/package/babel-plugin-transform-object-rest-spread

Then add it to .babelrc:

"plugins": [
    "babel-plugin-transform-object-rest-spread",
  ],


### 2. `newwebpack No data received ERR_EMPTY_RESPONSE` 错误

给 webpack-dev-server 添加 --host xxxxxxxx(设好的 host 映射，或者指定的域名)
