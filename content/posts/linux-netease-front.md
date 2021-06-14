---
title: "解决 Linux 下的网易云音乐字体过小问题"
date: 2021-06-14T14:53:55+08:00
categories:
  - 专题
tags:
  - "Linux"
---

## 问题

linux 下网易云音乐，是不错的选择，奈何自带的字体实在太小。

## 解决方案

使用编辑器修改 `/opt/netease/netease-cloud-music/netease-cloud-music.bash` 的内容，在最后一行中添加 `--force-device-scale-factor=xxx`。 xxx 是字体放大的倍数，例如我的就是： 1.2 。具体代码如下所示。

*注* 需要 root 权限

`sudo emacs /opt/netease/netease-cloud-music/netease-cloud-music.bash`

新的脚本应该如下所示：

``` shell
#!/bin/sh
HERE="$(dirname "$(readlink -f "${0}")")"
export LD_LIBRARY_PATH="${HERE}"/libs
export QT_PLUGIN_PATH="${HERE}"/plugins
export QT_QPA_PLATFORM_PLUGIN_PATH="${HERE}"/plugins/platforms
exec "${HERE}"/netease-cloud-music --force-device-scale-factor=1.2 $@
```
