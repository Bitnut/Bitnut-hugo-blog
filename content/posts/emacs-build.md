---
title: "编译安装 emacs"
date: 2021-02-19T21:53:55+08:00
tags:
  - "emacs"
  - GNU
  - "linux"
---

### 走个流程

1. 源码下载

到 emacs 的[官网](https://www.gnu.org/software/emacs/download.html)下载安装包
当我写这篇博客的时候，emacs 的最新稳定版是 27.1

2. 解压安装包

```shell
xz -d emacs-27.1.tar.xz
tar -xvf emacs-27.1.tar
```
3. 配置安装选项

`./configure --prefix=/opt/emacs/ --with-mailutils --with-pop >/dev/null 2>&1`

可能会遇到一些依赖保错

可以执行：

* X toolkit 的报错：

`sudo apt-get install build-essential texinfo libx11-dev libxpm-dev libjpeg-dev libpng-dev libgif-dev libtiff-dev libgtk2.0-dev libgtk-3-dev libncurses-dev libxpm-dev automake autoconf`

* gnutls 的报错：

`sudo apt-get install gnutls-dev`

4. 执行 make

`make && make install`

如果遇到依赖问题：

`sudo apt-get install make`

5. 添加软连接

ln -s /opt/emacs/bin/emacs /usr/bin/emacs

下面给出我写好的一个自用脚本：

### 脚本

```shell

#!/bin/bash

packageArr=""
package=""
unzipFile=""
targetDir=""
files=""

function getFile() {


    echo "Getting emacs packages";
    files=$(ls $1 | grep -E 'emacs.*xz|emacs.*tar');
    if [ ${#files} -eq 0 ];then echo "cannot find emacs release packages"; exit 1; fi
    packageArr=()
    for file in $files
    do
        packageArr[${#packageArr[@]}]=$file
    done
    package=${packageArr[0]};
    unzipFile=${package%\.xz}
    targetDir=${unzipFile%\.tar}
    echo "get eamcs packages complete";
}

getFile

echo "unziping package" ${package}
echo "..."
# xz -d ${package}

echo "unziping tar file" ${unzipFile}
echo "..."
tar -xvf ${unzipFile} > /dev/null

cd ${targetDir}
echo "configuring emacs to /opt/emacs"
echo "..."


./configure --prefix=/opt/emacs/ --with-mailutils --with-pop >/dev/null 2>&1

echo "Doing make and make install"

make

make install

echo "Adding soft link"

ln -s /opt/bin/emacs /usr/bin/emacs

```
