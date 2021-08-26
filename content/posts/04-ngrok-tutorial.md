---
title: 内网映射工具ngrok
author: MasterPan
tags:
  - ngrok
categories:
  - tools
abbrlink: 44878
date: 2018-09-04 11:12:00
---
### 前言
做过微信公众号开发的小伙伴就应该知道，微信必须要部署在外网才能调试，很是不方便，这就需要一款内网映射工具了，这里介绍[ngrok](https://ngrok.com)，有免费版，操作简单。
### 准备
下载[ngrok](https://ngrok.com/download)，如果官网无法访问，可使用以下链接下载：
windows：[下载链接](https://9527.blog/ngrok/windows/ngrok-stable-windows-386.zip)
osx：[下载链接](https://9527.blog/ngrok/osx/ngrok-stable-darwin-amd64.zip)
linux：[下载链接](https://9527.blog/ngrok/linux/ngrok-stable-linux-amd64.zip)
### 正文
解压安装包到指定目录，cd到这个目录，执行
``` shell
# windows
$ ngrok.exe http 8080

# osx/linux
$ ./ngrok http 8080
```
运行成功
``` shell
ngrok by @inconshreveable     (Ctrl+C to quit)

Session Status                online
Session Expires               7 hours, 56 minutes
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://4983b8b1.ngrok.io -> localhost:8080
Forwarding                    https://4983b8b1.ngrok.io -> localhost:8080

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```
外网访问`http://4983b8b1.ngrok.io`，即访问内网`http://localhost:8080`，端口可在命令中修改。