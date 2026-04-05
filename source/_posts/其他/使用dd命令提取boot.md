---
title: 使用dd命令提取boot
description: 一直忘记怎么操作，所以写这篇教程方便查找顺便防止忘记（说白了不就是不会）
categories: 学习记录
abbrlink: e8bec2d1
date: 2025-09-22 20:19:23
tags: 
 - 安卓
---

## 教程开始

- **首先在手机上启用开发者选项，并打开以root身份的adb调试**

设置导航到关于手机，连续点击版本号直到出现“你已启用开发者选项”
然后找到开发者选项，找到“adb调试”和“以root身份的调试”并打开
如果开发者选项中没有“以root身份的调试”，那么你首先得获取root权限

- **手机连接电脑，然后在电脑上以打开adb**

win+R输入cmd唤出命令提示符，然后输入`adb devices`来检测设备：

```shell
C:\Users\29395>adb devices
List of devices attached
721QAD4S44CXQ   device    #检测到设备会像这样子显示
```

> 要事先将adb所在目录添加到系统变量才能直接在cmd中打开adb，如果没有添加的话必须先定位到adb所在目录

检测到设备后，输入`adb root`以root身份打开adb

```bash
C:\Users\29395>adb root
restarting adbd as root
```

> 如果开发者选项中没有“以root身份的调试”，可以不用做这一步

- **进入shell，然后找到boot所在的目录**

输入`adb shell`，进入shell

> 若上一步没有用root身份打开adb，进入shell后输入su就可以让shell获取root权限（前提是你已经root）

然后输入`cd /dev/block/by-name`进入boot所在目录
再输入`ls -l boot`查看boot的具体路径

```shell
C:\Users\29395>adb shell
m1721:/ # cd /dev/block/by-name
m1721:/dev/block/by-name # ls -l boot
lrwxrwxrwx 1 root root 21 1970-01-01 09:47 boot -> /dev/block/mmcblk0p21        #这里的/dev/block/mmcblk0p21就是boot的真实路径
m1721:/dev/block/by-name #
```

> 现在较新的手机都是Vab分区，所以会存在boot_a和boot_b，按需提取即可

- **使用dd命令提取boot**

复制boot的路径，输入`dd if=<复制的boot路径> of=/sdcard/boot.img`来提取

```shell
m1721:/dev/block/by-name # dd if=/dev/block/mmcblk0p21 of=/sdcard/boot.img
65536+0 records in
65536+0 records out
33554432 bytes (32 M) copied, 0.625224 s, 51 M/s
m1721:/dev/block/by-name #
```

这个时候在手机上打开文件管理器，就能在根目录找到刚刚提取的`boot.img`了