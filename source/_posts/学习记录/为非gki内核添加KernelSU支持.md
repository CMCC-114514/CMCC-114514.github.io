---
title: 为非gki内核添加KernelSU支持
description: 手把手教你如何集成kernelsu
categories: 学习记录
abbrlink: 57133
date: 2025-09-09 22:35:03
tags:
- 转载
- 安卓刷机
- 内核编译
---

## 前言

在Non-gki内核中添加KernelSU的前提得是编译好的内核能够正常开机（废话+1），所以本文也将会详细介绍安卓内核的编译过程以及在编译过程中可能遇到的情况.
​写这个文章不是因为我技术有多好，只是因为我刚开始搞这个跟个伞兵一样找不到路，所以现在稍微明白点了就写下来给想搞这个的看看，也是为了防止我自己以后忘了。。奶子不大好使记不住东西

> 如果你只是想编译内核而不是使用kernelsu，你可以忽略本文中拉去kernelsu和修改内核配置的部分

### 环境要求

一台Linux设备（虚拟机，Docker，WSL，甚至是手机上的Terumx都是可以的），我这里以WSL2，Ubuntu 22.04.2 LTS为例

一部可以正常使用的安卓手机（废话+2）

可以让你连接到github和google的方法（你懂的§(￣▽￣)§）

最后再带上你聪明的小脑袋瓜，粗发！

### 准备工作

- 首先需要下载所需要的依赖，跟坨屎一样多，直接复制执行就好

```shell
sudo apt-get install git ccache automake flex lzop bison gperf build-essential zip curl zlib1g-dev g++-multilib libxml2-utils bzip2 libbz2-dev libbz2-1.0 libghc-bzlib-dev squashfs-tools pngcrush schedtool dpkg-dev liblz4-tool make optipng maven libssl-dev pwgen libswitch-perl policycoreutils minicom libxml-sax-base-perl libxml-simple-perl bc libc6-dev-i386 lib32ncurses5-dev libx11-dev lib32z-dev
```

- 下载适合自己手机的内核build

这个东西需要自己去Github找，一般为`(android)_kernel_<手机厂商>_<cpu代号>`

```bash
# 以一加8T为例，高通骁龙865代号为sm8250，那么内核仓库名就可能是
android_kernel_oneplus_sm8250
kernel_oneplus_sm8250
```

我这里的例子是Handelinkernel-v2.6，我在氢的时候最喜欢的一个内核（同时也是最喜欢的一个版本）

```bash
# 仓库地址
https://git.drg.fi/drgreen/android_kernel_oneplus_sm8250.git
```

- 下载所需的交叉编译器

​ 由于这个内核自带了编译脚本，所以我们就根据脚本里的的clang版本下载(至于编译脚本我后面再说)
**当然，其实我更推荐第三方的*clang*，因为比较省事**比如：[ZyC clang](https://github.com/ZyCromerZ/Clang) 、[proton-clang](https://github.com/kdrag0n/proton-clang) 或者[clang-build-catalogue](https://github.com/Neutron-Toolchains/clang-build-catalogue)
使用方法也都差不多，当然你也可以使用谷歌官方的clang不过第三方有的更省事直接用就完事了

```bash
# 以我为例，我把它们分别放在了
~/android/clang-r407598b
~/android/toolchain
```

## 拉取资源

- 拉取内核源码

```shell
# cd到用户文件夹
cd ~

# 拉取内核,有的内核还有submodules，所以直接加上--recurse-submodules
git clone --recurse-submodules https://git.drg.fi/drgreen/android_kernel_oneplus_sm8250.git
```

- 拉取交叉编译工具
  **aosp-clang和第三方clang选其中一个就好**

```shell
# aosp-clang

# 新建文件夹让后直接curl
mkdir ~/android
cd ~/android
curl <需要拉取的clang连接>
# 具体版本在这找
# https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/+/refs/heads/main
```

按这个图片找对应版本
![](images/passage2/step2.1.png)

找到需要的版本后看下图
![](images/passage2/step2.2.png)

```shell
# 第三方clang(以Zyclang举例)，在github仓库的release

# 复制链接直接在当前用户目录下curl
mkdir ~/android
cd ~/android
# 记得选择对应版本
curl <下图获取的连接>
```

![](images/passage2/step2.3.png)

- 拉取kernelsu

```bash
cd ~/<内核文件夹>

# 在内核文件夹的根目录执行以下命令
curl -LSs "https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh" | bash -s v0.9.5
```

> KernelSU 1.0 及更高版本已经不再支持非 GKI 内核，最后的支持版本为 v0.9.5，请注意使用正确的版本。

## 修改配置

- 修改配置文件

```bash
cd ~/android_kernel_oneplus_sm8250/arch/arm64/configs

# 此时你所需要的配置文件可能在./defconfig
# 但大概率可能会在./vendor/<配置文件>

# 以本内核为例
vi /vendor/kona-perf_defconfig

# 在配置文件最底下加上
CONFIG_KPROBES=y
CONFIG_HAVE_KPROBES=y
CONFIG_KPROBE_EVENTS=y
```

## 编译内核

```shell
# 进去内核的根目录
cd ~/android_kernel_oneplus_sm8250

# 新建一个shell文件
vim ./build.sh

# 输入以下内容
# shell开始
#!/bin/bash

export ARCH=arm64
export SUBARCH=arm64

args="-j$(nproc --all) \
O=out \
CC=clang \
ARCH=arm64 \
LD=ld.lld \
LLVM=1 \
LLVM_IAS=1 \
CROSS_COMPILE=aarch64-linux-gnu- \
CROSS_COMPILE_COMPAT=arm-linux-gnueabi- "        #根据需要可将COMPAT改为ARM32

export PATH="$HOME/<clang文件夹路径>/bin:$PATH"
make ${args} vendor/kona-perf_defconfig
make ${args}
# shell结束

# 保存后退出，给shell执行权限
chmod a+x ./buids.sh
# 执行,用source方便报错后调试
source ./build.sh
```

## 试验

如果你足够幸运的话，那么你现在已经成功一半了
去内核文件夹的out/arch/arm64/boot/文件夹看看你的成果
把*Image*放入Anykernel中然后压缩刷入(不同内核压缩方式不同，比如：Image.gz）
[Anykernel的使用方法](https://github.com/tiann/KernelSU/discussions/952#discussion-5623210)

## 恭喜你，看来你不是幸运儿

如果你此时将内核刷入后发现各种问题，比如黑屏，重启，高通崩溃（甚至你的out路径里根本就没有出现镜像文件），又或者你编译时报错，所以我将会继续为你排忧解难

- 如果你刷入内核后手机不能正常启动，你可以需要按照术哥（kernelsu的作者，就是那只皮卡丘）的办法，放弃使用kprobe集成的办法，转去修改那么几个内核文件，具体请参考 https://kernelsu.org/zh_CN/guide/how-to-integrate-for-non-gki.html 中手动修改内核源码部分的内容。在修改完成后再次拉取kernelsu(只是为了执行那个脚本，当然你也可以在内核根文件夹内执行kernelsu的脚本)

————————————————
版权声明：本文为CSDN博主「果呆皮」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：[为非gki内核添加KernelSU支持](https://blog.csdn.net/qq_43283565/article/details/137374337)