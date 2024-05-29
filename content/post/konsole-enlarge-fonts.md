---
title: "Linux Konsole 控制台字体大小设置"
date: 2024-05-28T21:53:55+08:00
categories:
tags:
  - "Linux"
---

最近发现自己使用的控制台字体过小，导致看得很难受。这里做一个简单的记录。


我使用的是 Konsole，它有一个profile的概念。系统默认的 profile 没有办法 edit，因此需要新建一个 profile，并编辑这个 profile 的参数，来达到修改的目的。


鼠标右键点击选择创建 Profile 可以创建新的 Profile，Create New Profile->Appreance 选择合适的字体和大小等即可完成 Profile 的创建。


最后点击 Setting -> Configure Konsole -> Profiles -> Set as Default 可以设置当前profile 为默认profile，即可对所有新的 tab 使用新创建的 profile。
