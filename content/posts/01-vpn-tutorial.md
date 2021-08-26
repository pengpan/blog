---
title: 用搬瓦工VPS搭建SS及SSR科学上网翻墙详细教程
author: MasterPan
tags:
  - vpn
categories:
  - tools
abbrlink: 19789
date: 2018-08-30 14:17:00
---
### 前言
由于国内网络限制(墙)，导致很多国外的网站不能访问，如google，facebook，twitter等。一般用户会选择购买VPN软件，如xx加速器，价格贵还不说，速度也慢得可怜。既然这样，为何不自己搭建一个VPN呢？价格也不贵，最低19美元一年，折合人民币10元一个月，网络稳定速度快。还等什么，接下来我就手把手教大家如何搭建VPN科学上网。

### 正文
#### 购买VPS
国外的VPS有很多选择，如搬瓦工，[vultr](https://www.vultr.com/?ref=7524357)，linode等，这里就不作过多介绍，直接用[搬瓦工](https://bwh1.net)
##### 选择方案

| 硬盘  | 内存   | CPU           | 流量      | 价格                | 链接                                                  |
| ----- | ------ | ------------- | --------- | ------------------- | ----------------------------------------------------- |
| 10 GB | 512 MB | 1x Intel Xeon | 500 GB/mo | $19.99 USD Annually | [购买链接](https://bwh1.net/aff.php?aff=17389&pid=43) |

这个是所有方案中最便宜的一个了，对于平时查资料，逛YouTube是足够用了，但是这个方案经常会出现库存不足，不过没关系，可以加10美元换[另一个方案](https://bwh1.net/aff.php?aff=23114&pid=56)，配置一样，流量是500G([CN2线路](https://www.idcbest.com/datanews/11001343.html))，网速会更快。19.9美元的方案也可以转CN2(下面会介绍)，但流量会降至500GB的1/3，183G对于平时上网也是足够用了。

##### 注册账号并购买
![](/images/vpn-tutorial/20180831113657.png)
优惠码：**BWH26FXH3HIQ**
![](/images/vpn-tutorial/20180831115227.png)
填写注册信息并提交订单
![](/images/vpn-tutorial/20180831122846.png)
最后支付宝完成付款即可，邮箱会收到VPS的用户名密码端口等信息。
##### 控制面板开启SS或SSR
![](/images/vpn-tutorial/20180831124113.png)
![](/images/vpn-tutorial/20180831124332.png)
![](/images/vpn-tutorial/20180831124946.png)
这里可以直接开启SS或SSR，但也可以通过脚本搭建SSR，下面讲到
![](/images/vpn-tutorial/20180831124950.jpg)

#### 搭建SSR (脚本)
这里是通过Xshell连接VPS远程安装
##### 登录服务器
![](/images/vpn-tutorial/20180831125728.png)
![](/images/vpn-tutorial/20180831125909.png)
``` shell
# 测试VPS性能
$ wget -qO- git.io/superbench.sh | bash
```
##### 安装脚本
使用root用户登录，运行以下命令：
``` shell
$ wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
$ chmod +x shadowsocksR.sh
$ ./shadowsocksR.sh 2>&1 | tee shadowsocksR.log
```
安装完成后，脚本提示如下：
```
Congratulations, ShadowsocksR server install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Protocol         :your_protocol
Your obfs             :your_obfs
Your Encryption Method:your_encryption_method

Welcome to visit:https://shadowsocks.be/9.html
Enjoy it!
```

#### 科学上网
##### 转CN2线路
转CN2会更换IP，所以一定要做好备份，不转也是不影响上网的，可跳过
![](/images/vpn-tutorial/20180831135535.png)

##### 客户端下载
IOS：App Store搜索"**SsrConnectPro**"安装即可
Android：[下载链接](https://9527.blog/shadowsocks/android/com.github.shadowsocks.apk)
OSX: [下载链接](https://9527.blog/shadowsocks/osx/ShadowsocksX-NG.1.7.1.zip)
Windows: [下载链接](https://9527.blog/shadowsocks/windows/Shadowsocks-4.1.1.zip)

##### 客户端配置
这里以Windows为例，其他平台也是这样配置的，就不多说了
把那些信息对应的填上去就完事了
![](/images/vpn-tutorial/20180831130848.png)
最后就可以愉快的上网了！