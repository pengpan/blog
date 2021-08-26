---
title: 内网映射工具frp
author: MasterPan
tags:
  - frp
categories:
  - tools
abbrlink: 11402
date: 2018-10-11 22:21:00
---
### 前言
之前有写过一篇[内网映射工具ngrok](/posts/44878.html)，但是ngrok的速度不是很快，而且每次启动的生成二级域名都会变化，要长期使用不是很方便，下面介绍一款稳定快速的内网映射工具frp
> frp是国内大神写的开源作品，目前start接近16K
> 开源地址：[https://github.com/fatedier/frp](https://github.com/fatedier/frp)
> 中文文档：[https://github.com/fatedier/frp/blob/master/README_zh.md](https://github.com/fatedier/frp/blob/master/README_zh.md)

### 正文
#### 配置服务端
首先在服务机(需要外网能访问)下载对应系统的[软件包](https://github.com/fatedier/frp/releases)，解压并编辑`frps.ini`文件
如果官方软件包下载慢，可使用以下链接下载：
windows：[下载链接](https://9527.blog/frp/windows/frp_0.21.0_windows_amd64.zip)
osx：[下载链接](https://9527.blog/frp/osx/frp_0.21.0_darwin_amd64.tar.gz)
linux：[下载链接](https://9527.blog/frp/linux/frp_0.21.0_linux_amd64.tar.gz)
``` shell
# frps.ini
[common]
bind_port = 7000
```
启动
``` shell
$ nohup ./frps -c ./frps.ini &
```

#### 配置客户端
同样在客户机(内网)下载对应系统的软件包，解压并编辑`frpc.ini`文件
``` shell
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```
注：x.x.x.x为服务机公网IP
启动
``` shell
$ nohup ./frpc -c ./frpc.ini &
```

#### 通过ssh访问内网机器
``` shell
$ ssh -oPort=6000 root@x.x.x.x
```

#### 结束语
frp的功能远不止这些，更多配置请查看[官方文档](https://github.com/fatedier/frp/blob/master/README_zh.md)
