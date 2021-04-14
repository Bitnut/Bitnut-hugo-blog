---
title: "Linux 下的 ps 命令和常用组合"
date: 2021-02-19T21:53:55+08:00
categories:
  - 专题
tags:
  - "Linux"
---

## 前言

总结整理一下简单、常用的 ps 命令用法。

## 一、查看进程

命令： ps

命令解释： 全称是 `process status` ，使用它相当于在 win 下的 `任务管理器`。

常用命令参数：
-a 显示同一终端下的所有程序
-e 等于“-A”
-e  显示环境变量
-f  显示程序间的关系
-r  显示当前终端的进程
-u  指定用户的所有进程

常用命令组合：

-au 显示较详细的资讯
-aux 显示所有包含其他使用者的行程

-C <命令> 列出指定命令的状况
--lines<行数> 每页显示的行数
--width<字符数> 每页显示的字符数
--help 显示帮助信息
--version 显示版本显示



## 二、查看进程（树结构）

命令： pstree

命令参数：

-p 为显示进程识别码，最后加上用户名。

``` shell
picher@pichers-laptop:~$ pstree -p picher | grep emacs
              |-gnome-terminal-(3023)-+-bash(3030)-+-emacs(5517)-+-bash(28980)
              |                       |            |             |-{emacs}(5518)
              |                       |            |             |-{emacs}(5519)
              |                       |            |             `-{emacs}(5520)
```

可以看到，emacs(5517)这个进程共启动了 4 个子线程，加上主线程共 5 个线程。

## 三、杀死进程

命令：kill

常用组合：

* kill -STOP [pid]
发送 SIGSTOP (17,19,23)停止一个进程，而并不消灭这个进程。

* kill -CONT [pid]
发送 SIGCONT (19,18,25)重新开始一个停止的进程。

* kill -KILL [pid]
发送 SIGKILL (9)强迫进程立即停止，并且不实施清理操作。

* kill -9 -1
终止你拥有的全部进程。

## 四、完整操作

* 显示所有进程信息

命令：　　ps -A

* 显示指定用户信息

命令：　　ps -u root

* 显示所有进程信息，连同命令行

命令：　　ps -ef


* 查找特定进程

命令：　　ps -ef|grep emacs

* 将目前属于您自己这次登入的 PID 与相关信息列示出来

命令：　　ps -l

* 列出目前所有的正在内存当中的程序

命令：　　ps aux

输出：

``` shell
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.3 185228  3100 ?        Ss   May23   0:08 /lib/systemd/systemd --system --deserialize 21
root          2  0.0  0.0      0     0 ?        S    May23   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        I<   May23   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        I<   May23   0:00 [mm_percpu_wq]
root          7  0.0  0.0      0     0 ?        S    May23   0:04 [ksoftirqd/0]
root          8  0.0  0.0      0     0 ?        I    May23   0:03 [rcu_sched]
root          9  0.0  0.0      0     0 ?        I    May23   0:00 [rcu_bh]
root         10  0.0  0.0      0     0 ?        S    May23   0:00 [migration/0]
root         11  0.0  0.0      0     0 ?        S    May23   0:00 [watchdog/0]
```

#### 参数解释：

USER：该 process 属于那个使用者账号的

PID ：该 process 的号码

%CPU：该 process 使用掉的 CPU 资源百分比

%MEM：该 process 所占用的物理内存百分比

VSZ ：该 process 使用掉的虚拟内存量 (Kbytes)

RSS ：该 process 占用的固定的内存量 (Kbytes)

TTY ：该 process 是在那个终端机上面运作，若与终端机无关，则显示 ?，另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。

STAT：该程序目前的状态，主要的状态有

R ：该程序目前正在运作，或者是可被运作

S ：该程序目前正在睡眠当中 (可说是 idle 状态)，但可被某些讯号 (signal) 唤醒。

T ：该程序目前正在侦测或者是停止了

Z ：该程序应该已经终止，但是其父程序却无法正常的终止他，造成 zombie (疆尸) 程序的状态

START：该 process 被触发启动的时间

TIME ：该 process 实际使用 CPU 运作的时间

COMMAND：该程序的实际指令



* 可以用 | 管道和 more 连接起来分页查看

命令：　　ps -aux | more

* 把所有进程显示出来，并输出到 ps001.txt 文件

命令：　　ps -aux > ps001.log

* 输出指定的字段

命令：　　 ps -o pid,ppid,pgrp,session,tpgid,comm > ps001.log
