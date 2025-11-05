---
title: 用gcc编译老内核（步骤篇）
date: 2025-09-09 16:00:19
description: 用gcc编译老内核，比如4.4.x版本的内核
categories: 教程
abbrlink: 50843
tags:
  - 内核编译
  - 安卓
---

## 准备条件：

- 一台linux虚拟机（比如我用的Ubuntu 22.04)
- 一部比较老的测试用设备（比如我手上的米板4）
- 一个聪明的大脑

## 安装gcc和各种依赖

依赖部分是我从 [另一个教程](https://blog.csdn.net/qq_43283565/article/details/137374337) 里搞来的

```bash
sudo apt-get install git ccache automake flex lzop bison gperf build-essential zip curl zlib1g-dev g++-multilib libxml2-utils bzip2 libbz2-dev libbz2-1.0 libghc-bzlib-dev squashfs-tools pngcrush schedtool dpkg-dev liblz4-tool make optipng maven libssl-dev pwgen libswitch-perl policycoreutils minicom libxml-sax-base-perl libxml-simple-perl bc libc6-dev-i386 lib32ncurses5-dev libx11-dev lib32z-dev
```

gcc是编译内核必须的工具链，没有安装gcc就绝对不能编译

安装gcc aarch64和arm32的代码

```bash
#安装gcc aarch64
sudo apt install gcc-aarch64-linux-gnu

#安装gcc arm32
sudo apt install gcc-arm-linux-gnueabi
```

安装后输出版本号确认

```bash
aarch64-linux-gnu-gcc --version
arm-linux-gnueabi-gcc --version
```

## 获取内核源代码

源代码一般在github就能找到
先进入 [github](https://github.com/) 官网，然后在搜索栏中输入android_kernel_(品牌)_(机型)，回车搜索

比如小米平板4的品牌是xiaomi，机型是clover：
![](/images/passage1/step1.1.png)

然后挑选你中意的内核点进去，再点击那个绿色的"Code"按钮复制clone链接
就是那个两个方框连起来那个链接
![](/images/passage1/step1.2.png)

然后在虚拟机里唤出终端（ctrl + alt + T），输入代码

```bash
git clone (你复制到的代码)
```

一切顺利的话，git就会将内核源代码clone到`home/android_kernel_(品牌)_(机型)`了
当然，如果clone过程报错，挂个梯子再试试
如果还是出现问题，问问ai更好

## 配置编译环境

我试过很多的教程，它们用的都是编译脚本，可是全都失败了
我问deepseek之后才勉强编译出来一个内核（虽然没有开机）
如果之后我用编译脚本成功编译出来内核，我应该会修改这一部分

现在先进入内核根目录吧：

```bash
cd ~/android_kernel_(品牌)_(机型)
```

首先按照目标架构选择工具链：

```bash
# 对于ARM64 (aarch64)
export ARCH=arm64
export CROSS_COMPILE=aarch64-linux-gnu-

# 对于ARM32
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabi-
```

然后配置内核

```bash
make defconfig  # 使用默认配置
```

## 开始编译内核

```bash
make -j$(nproc)  # 使用所有可用核心编译
```

如果你非常非常幸运，那么这一步你将得到编译完成的内核
一般情况下，编译完成的内核存放在：
`~/arch/arm64/boot/Image` - 未压缩的内核镜像
`~/arch/arm64/boot/Image.gz` - 压缩的内核镜像
如果编译的是arm32的内核，镜像文件可能为`zImage`

但实际上你很不幸，编译过程中出现了很多错误
而且因为报错内容又臭又长，你基本看不懂也不会想去研究
下一篇我就会编译过程中碰到的所有错误和解决办法列出来

## 可用的替代方案：使用Android预构建工具链

如果系统GCC版本太新，导致出现各种问题，考虑使用Android官方工具链：

```bash
# 下载Android NDK
wget https://dl.google.com/android/repository/android-ndk-r21e-linux-x86_64.zip
unzip android-ndk-r21e-linux-x86_64.zip

# 使用NDK工具链
export PATH=$PATH:/path/to/android-ndk-r21e/toolchains/llvm/prebuilt/linux-x86_64/bin
```