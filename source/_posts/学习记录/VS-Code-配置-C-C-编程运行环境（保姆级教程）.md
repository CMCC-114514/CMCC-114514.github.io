---
title: VS Code 配置 C/C++ 编程运行环境（保姆级教程）
hidden: true
description: 教你安装并使用vscode
categories: 学习记录
tags:
  - 编辑器
  - vscode
  - ide
  - c++
abbrlink: a57909c8
date: 2025-11-24 22:54:55
---

在本教程中，将会安装 Visual Studio Code（后简称 VS Code），并在 VS Code 中安装 C/C++ 相关插件， 同时也将 VS Code 配置为使用 MinGW-W64 中的 GCC C/C++ 编译器（gcc/g++）和 GDB 调试器来创建在 Windows 上运行的程序。配置 VS Code 后，你将编写、编译、运行和调试大多数的 C/C++ 程序。

本教程所有参考内容均来自[Documentation for Visual Studio Code](https://code.visualstudio.com/docs)。

## 一、软件下载

1. **下载 VS Code 安装工具**
   
   官方下载链接：[Visual Studio Code - Code Editing. Redefineda](https://code.visualstudio.com/)
   
   ![]()
   
   直接点“Download for Windows”就可以进行下载。

2. **下载 MinGW-W64**
   
   MinGW-W64 可以去[MinGW-w64](https://www.mingw-w64.org/)的官网下载，也就可以直接去 MinGW-W64 的 [GitHub](https://so.csdn.net/so/search?q=GitHub&spm=1001.2101.3001.7020) 上下载。由于在官网下载容易下错，所以我这里给一个 GitHub 的链接，也是在 VS Code 上提供的链接。（官方认证，绝对没错！）
   
   **MinGW-W64下载链接：**[Releases · msys2/msys2-installer (github.com)](https://github.com/msys2/msys2-installer/releases/)
   
   进入链接后，可以看到历史版本的更替，截至本教程编写日期，最新版本为 [2024-01-13](https://github.com/msys2/msys2-installer/releases/tag/2024-01-13) 的版本，单击日期跳转至下载窗口。
   
   ![]()
   
   如下图所示，选择`msys2-x86_64-20240113.exe`（记住前缀是 msys2-x86_64 就行，后面是日期），点击后面的下载标志。
   
   ![]()

## 二、安装 VS Code

双击运行`VSCodeUserSetup-x64-1.87.2.exe`。

![]()

如果出现如下弹窗，单击`运行`即可。

![]()

选择`我同意此协议`并单击`下一步`。

![]()

这里提示 VS Code 的安装位置，我只有一个 C 盘（现在的固态硬盘可以选择不分区），所以选择默认默认路径，直接点击`下一步`。当然，你的电脑硬盘要是有分区的话，可以选择其他路径。

![]()

这一步是创建快捷方式的名字，可以输入其他名字，下面的选项如果不勾选，就会把快捷方式添加到开始菜单中。我选择默认，直接点`下一步`。

![]()

默认只有最后两项被选中，我全部选上，单击`下一步`。

![]()

最后再确认一下信息，确认之后直接安装即可。

![]()

大概一分钟左右就可以安装完成。

![]()

安装完成后，先暂时不运行 VS Code，把勾选去掉，点击`完成`。

![]()

## 三、安装 MinGW-W64 及配置环境变量

双击`msys2-x86_64-20240113.exe`运行安装程序。

![]()

> [!CAUTION]
> 
> **请注意，MSYS2 需要 64 位 Windows 8.1 及以上版本。**

此界面直接点击`下一步`。

![]()

选择安装的路径，我这里选择默认，同学们可以根据自己的情况修改路径，之后点击`下一步`。

---

版权声明：本文为CSDN博主「Grayson Zheng」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_42417071/article/details/137438374
