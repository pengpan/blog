---
title: 利用kcptun加速你的SSR
author: MasterPan
tags:
  - kcptun
categories:
  - tools
abbrlink: 64408
date: 2018-12-30 22:30:00
---

### 前言
> Kcptun介绍

- Kcptun 是一个非常简单和快速的，基于 KCP 协议的 UDP 隧道，它可以将 TCP 流转换为 KCP+UDP 流。而 KCP 是一个快速可靠协议，能以比 TCP 浪费10%-20%的带宽的代价，换取平均延迟降低 30%-40%，且最大延迟降低三倍的传输效果。
- Kcptun 是 KCP 协议的一个简单应用，可以用于任意 TCP 网络程序的传输承载，以提高网络流畅度，降低掉线情况。由于Kcptun 使用 Go 语言编写，内存占用低（经测试，在64M内存服务器上稳定运行），而且适用于所有平台，甚至 Arm 平台。

> Kcptun工作示意图

![](/images/ssr-use-kcptun/kcptun.png)

> KCP 协议：[https://github.com/skywind3000/kcp](https://github.com/skywind3000/kcp)
> Kcptun 项目地址：[https://github.com/xtaci/kcptun](https://github.com/xtaci/kcptun)

### 正文

#### kcptun服务端配置
服务器操作系统: CentOS7
需先安装好Shadowsocks服务端，并启动它

- 一键脚本安装
``` shell
$ wget --no-check-certificate https://github.com/kuoruan/shell-scripts/raw/master/kcptun/kcptun.sh
$ chmod +x ./kcptun.sh
$ ./kcptun.sh
```

- 保存客户端配置文件
``` shell
可使用的客户端配置文件为:
{
  "localaddr": ":11226",
  "remoteaddr": "0.0.0.0:29900",
  "key": "xxxxx",
  "crypt": "aes",
  "mode": "fast",
  "mtu": 1350,
  "sndwnd": 512,
  "rcvwnd": 512,
  "datashard": 10,
  "parityshard": 3,
  "dscp": 0,
  "nocomp": false,
  "quiet": false
}
```
    如果忘记了，可使用命令`./kcptun.sh show`再次查看

#### kcptun客户端配置

##### windows
1. 下载[windows客户端](https://9527.blog/kcptun/kcptun-windows-client-20181230.zip)
2. 配置客户端
  1. 将服务端生成的客户端配置粘贴到`kcptun.json`中
  2. 运行`kcptun_gclient.exe`
  3. 添加客户端`client_windows_amd64.exe`，勾选使用配置文件，选择`kcptun.json`
  4. 启动
![](/images/ssr-use-kcptun/kcptun_client_config.png)
3. 配置Shadowsocks客户端
![](/images/ssr-use-kcptun/ss_config.png)

##### macos
1. 下载[macos客户端](https://9527.blog/kcptun/kcptun-darwin-client-20181230.zip)
2. 配置客户端
  1. 将服务端生成的客户端配置粘贴到`kcptun.json`中
  2. 运行`./client_darwin_amd64 -c kcptun.json`
3. 配置Shadowsocks客户端（同上）

#### 效果对比

##### 开启前
![](/images/ssr-use-kcptun/disable_kcptun.jpg)
##### 开启后
![](/images/ssr-use-kcptun/enable_kcptun.jpg)
##### 效果提升显著

#### 参考文档
[[v20]Kcptun 服务端一键安装脚本](https://blog.kuoruan.com/110.html)
[shadowsocks安装kcptun加速](http://blog.51cto.com/gordy/1974673)
