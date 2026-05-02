---
title: MQTT-MQTTX客户端的基本使用
hidden: false
categories:
  - 学习记录
  - MQTT学习
tags:
  - MQTT
  - 物联网
abbrlink: 3d2f4032
date: 2026-04-29 21:55:06
---

> 需要先学习《EMQX环境搭建》

## 一些基本概念

#### 客户端 

指任何运行MQTT客户端库（即MQTT开发工具包）的应用或设备

#### Broker

实现了MQTT协议的代理软件

#### 主题

存在于Broker中的字符串，用于对消息进行分类。发布消息到Broker就是发布消息到Broker的主题

## 安装MQTTX客户端

去官网根据自己的系统安装MQTTX即可，分为desktop版和cli版，后者为命令行界面

### desktop版使用方法

#### 新建连接

打开MQTTX客户端，点击侧边栏的加号添加连接，名称自定，服务器地址改为虚拟机地址，其他不需要修改，然后点击“连接”按钮创建连接

这里创建了两个连接，PUBLIC连接用于发送信息，SUB连接用于接收信息

![](E:\Code\CMCC-114514.github.io\source\images\3d2f4032\t1.png)

#### 添加订阅

在“连接”栏目中选择SUB连接，点击“添加订阅”按钮，输入订阅名（也就是填写Topic选项中的内容）之后点击确定，这样就添加了订阅

![](E:\Code\CMCC-114514.github.io\source\images\3d2f4032\t2.png)

#### 向订阅发送消息

在下图中的输入框（这个框在界面的右下角）中，Topic选项就是填写需要发送到的订阅名，下面输入的就是发送的信息

![](E:\Code\CMCC-114514.github.io\source\images\3d2f4032\t3.png)

### cli版使用方法

#### 建立连接

打开cmd，进入mqttx_cli.exe所在的位置，然后输入以下命令来搭建接收消息部分：

```cmd
mqttx_cli.exe sub -t 'b' -h 192.168.3.131 -p 1883 -v
```

命令中，`sub`  指订阅一个主题，`-t` 参数指订阅主题Topic，`-h` 参数指MQTT服务器Host的ip，`-p` 参数指端口号

然后再打开一个cmd窗口（之前那个窗口不要关闭），输入以下命令来发送消息到上文搭建的接收端：

```cmd
mqttx pub -t 'b' -q 0 -h 192.168.3.131 -p 1883 -m "hello mqttx"
```

命令中，`pub` 指向指定的主题发送信息，`-m` 参数指发送的信息内容

这时候，接收端就会收到来自发送端发送的消息：`hello mqttx` 了，同时还附带一些其他信息：

```cmd
mqtt-packet: Packet {
  cmd: 'publish',
  retain: false,
  qos: 0,
  dup: false,
  length: 17,
  topic: "'b'",
  payload: <Buffer 68 65 6c 6c 6f 20 6d 71 74 74 78>
}, topic: 'b', qos: 0, size: 11B
hello mqttx
```

### 网页客户端的使用

除了图形界面应用desktop和命令行应用cli，MQTTX还有网页端可以使用，在网页端进行测试的操作与在desktop上基本相同

在docker中部署MQTTX-Web的方法：

```bash
docker pull emqx/mqttx-web
docker run -d --name mqttx-web -p 80:80 emqx/mqttx-web
```

