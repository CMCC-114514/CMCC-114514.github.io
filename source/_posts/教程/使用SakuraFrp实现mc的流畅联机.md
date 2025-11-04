---
title: 使用SakuraFrp实现mc的流畅联机
categories: 教程
tags:
  - 内网穿透
  - Frp
  - 我的世界
description: 玩ow破防之后，我又开始玩起mc......
abbrlink: a4b13916
date: 2025-09-13 19:40
---
## 杂谈

搭建网站之前我有想过把我旧手机拿来当服务器
我还因此了解到内网穿透这东西
发现可以直接用Github直接搭网站之后就放一边了

这两天玩ow给我玩到破防，想着换个游戏玩玩
然后就想到mc联机
之前用Radmin LAN和KatoLAN搞虚拟局域网的效果不好
所以试试内网穿透，暑假的时候同学用过，效果还挺好
想到又可以拉朋（hei）友（nu）一起玩就很爽（

## 教程开始
首先进入 [Sakura Frp官网](https://www.natfrp.com/) 然后点击注册账号
注册好之后登入账号，进入管理面板
![](/images/passage3/step1.png)

然后点击用户--实名认证，按步骤进行实名认证（不实名认证无法使用内网穿透）
实名认证名额需要购买，价格为1元
![](/images/passage3/step2.png)

认证完成之后 [下载SakuraFrp启动器](https://www.natfrp.com/tunnel/download) 并安装
启动器界面应如下图所示
![](/images/passage3/step3.png)
这时回到官网的管理面板，点击用户--个人信息，复制访问密钥，再填入上图启动器中登录
![](/images/passage3/step4.png)

现在来创建隧道
点击服务--隧道列表，再点击创建隧道
![](/images/passage3/step5.png)
节点任意选（可能选离自己所在地更近的节点连接效果更好），然后选择TCP隧道
隧道名随便填
本地ip选择“本地主机(127.0.0.1)”
本地端口选择“[25565]Minecraft Java”
剩下的选项如果你看不懂就不要管，默认会设置好
然后点击“创建”
![](/images/passage3/step6.png)

然后打开mc，开启“对局域网开放”
这时候打开Frp启动器，开启隧道，进入日志界面
隧道会自动检测mc对局域网开放的端口并提示
``` log
TCP隧道启动成功
发现 Minecraft 局域网游戏, 本地 IP/端口 已经设置
使用 >>frp-bar.com:xxxxx<< 连接你的隧道
```
这时候你就可以将上面的链接复制给你的好友让他进入你的房间了

如果有更多问题，参阅官网的 [帮助文档](https://doc.natfrp.com/app/mc.html)