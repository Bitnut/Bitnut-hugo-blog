---
title: "代理实践纪录"
date: 2021-03-04T21:00:35+08:00
categories:
  - 工具
tags:
  - "GNU"
  - "Linux"
---

## 变化的 GFW 实在是令人很苦恼

在开发和学习的过程中，飞机始终是我们的好朋友。为了不在一些特别的时刻偶尔的失去他，我们也需要不断变化和学习和改进。


## 配置 Trojan 服务

* 一、 准备一台 VPS（境外），不需要购买域名和配置证书。

* 二、 VPS 安装 LINUX 系统.

注意，系统最好是以下几个。其他的发行版本没有做过测试。

    ubuntu 16.04+
    debian 9
    centos 7+

* 三、 替换镜像源

安装好后接着替换原本的镜像源为国内镜像源，如我使用 ubuntu 系统，则替换 `/etc/apt/sources.list` 下的内容为下面的任意一个地址，然后依次执行：

`sudo apt-get update`

`sudo apt-get upgrade`

```bash
#阿里源
    deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
    deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

```bash
    #网易源
    deb http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
```

```bash
    #清华源
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```

```bash
##中科大源
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
```
* 四、 安装 Trojan 服务端

使用 ssh 命令登录 VPS。 注意，安装前必须打开服务器的 80 和 443 端口。

在命令行执行命令： `wget -N --no-check-certificate https://raw.githubusercontent.com/mark-hans/trojan-wiz/master/ins.sh && chmod +x ins.sh && sudo bash ins.sh`

安装过程中提示“请选择证书模式”，选择”使用IP自签发证书”的模式。

* 五、启动Trojan服务端

安装完成后，使用命令 `systemctl start trojan-gfw` 启动 trojan 服务端, 同时可以用命令 `systemctl status trojan-gfw` 来检查 trojan 服务端的状态，如果状态为 `active(running)`, 证明 trojan 服务端已经启动。

* 六、拷贝服务端配置文件

trojan 服务端配置成功后，查看下 /home/trojan/ 目录下是否生成了 `client.json` 和 `ca-cert.pem` 两个文件。确认地址后，`exit` 退出 ssh 登录。

使用如下命令将配置文件拷贝到本地目录：

```shell
scp root@xxx.xxx.xxx.xxx:/home/trojan/ca-cert.pem ./

scp root@xxx.xxx.xxx.xxx:/home/trojan/client.json ./
```

* 七、安装 Trojan 客户端

执行如下命令直接下载安装包：

`wget https://github.com/trojan-gfw/trojan/releases/download/v1.14.1/trojan-1.14.1-linux-amd64.tar.xz`

解压后，将上一步中的配置文件放入目录中即可。

* 八、启动 Trojan 客户端

执行如下命令启动 Trojan 客户端：

`./trojan -c client.json `

此时，trojan 客户端会监听本地的 ` 127.0.0.1:1080` 监听代理请求。浏览器可以通过 Chrome SwitchyOmega 插件来配置代理设置。
