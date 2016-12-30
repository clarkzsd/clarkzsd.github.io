---
title: windows-ubuntu双系统问题
date: 2016-05-30 15:45:43
tags: [windows,linux]
categories: Tech
description: 装完ubuntu、windows双系统之后发现重启竟然默认是grub引导...

---

### 问题1：开机默认进入grub引导

装完ubuntu、windows双系统之后发现重启竟然默认是grub引导。

然后就去网上搜各种“如何换成windows引导”，大多都是需要一个系统安装盘来重建MBR。
太麻烦……一点都不优雅！
后来我就想到，我是通过UEFI来搞双系统的，开机按F12进入的时候有启动项如下：

1. Ubuntu
2. Windows Boot Manager
3. UEFI:LITEON

所以说，Ubuntu放在第一位所以才会默认启动ubuntu的grub引导……那我把Windows Boot Manager放在第一位不就行了？

然后继续Google如何修改UEFI的启动项顺序。
Google大法好，搜到一个叫`easyUEFI`的东西，可以修改默认启动项顺序。原版是要收费的，而我大天朝中文版竟然免费！
界面如下，上调Windows Boot Manager的顺序就Ok了。

![img](http://o6ljw8wcq.bkt.clouddn.com/%E6%9C%AA%E5%91%BD%E5%90%8D%E5%9B%BE%E7%89%87.png)

### 问题2：没有声音！！！

蛤，这个问题一搜一大片呀。有位机智的网友答道：这就是双系统常见的蜜汁bug，只要盒上电脑盖子再打开就行了。
*虽然不知道什么原理，但亲测有效！*