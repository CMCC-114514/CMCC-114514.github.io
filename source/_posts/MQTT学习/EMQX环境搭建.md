---
title: MQTT-EMQX环境搭建
hidden: false
categories:
  - 学习记录
  - MQTT学习
tags:
  - MQTT
  - 物联网
abbrlink: 7fc33a05
date: 2026-04-29 22:03:00
---

### 安装虚拟机

使用 VMware 或 VirtualBox 安装 Ubuntu 系统（推荐 20.04 或更高版本）

### 更新系统

```bash
sudo apt update && sudo apt upgrade -y
```

### 安装必要工具

```bash
sudo apt install curl -y
```

### 配置 EMQX APT 源

运行以下命令添加 EMQX 官方软件源：

```bash
curl -s https://assets.emqx.com/scripts/install-emqx-deb.sh | sudo bash
```

### 安装 EMQX

执行以下命令安装 EMQX：

```bash
sudo apt-get install emqx -y
```

### 启动 EMQX 服务并检查状态

```bash
sudo systemctl start emqx
sudo systemctl status emqx
```

如果启动成功，终端会显示类似 `EMQX is started successfully!` 的提示。

### 访问 Dashboard 管理控制台

在虚拟机的终端中使用以下命令获取虚拟机ip地址：

```bash
ifconfig
```

打开浏览器，访问以下地址：

```bash
http://<虚拟机IP>:18083
```

默认登录凭据： 用户名：admin 密码：public

首次登录后建议修改密码以增强安全性。

### EMQX常用端口号

1. 1883  TCP服务访问端口号
2. 8083  WebSocket服务
3. 8084  WebSocket secure服务
4. 8883  SSL/TLS端口
5. 18083  Broker操作台访问端口