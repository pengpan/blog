---
title: 使用VirtualBox安装CentOS7虚拟机
author: MasterPan
tags:
  - centos
categories:
  - linux
abbrlink: 8248
date: 2018-09-03 22:13:00
---
### 前言
现在大部分项目都是部署在Linux上，而我们开发环境一般是Windows和MacOS，在没有购买VPS的情况下，最好的方式就是在本地安装虚拟机了。网上的教程很多，但实际操作起来又不是那么回事，我之前有成功搭建过，并且设置了固定IP和联网，这里写一篇详细的教程，希望能帮助大家。
### 准备
VirtualBox：[下载链接](https://www.virtualbox.org/wiki/Downloads)
CentOS7：[下载链接](http://archive.kernel.org/centos-vault/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-Minimal.iso)
这里CentOS7用的是Minimal版，体积很小，没有多余的软件，纯命令系统。
### 正文
#### 新建虚拟机
![](/images/install-centos7-in-virtualbox/QQ20180909-212503@2x.png)
一路`Next`
点击`设置->存储`选择下载的镜像`CentOS-7-x86_64-Minimal-1708.iso`
![](/images/install-centos7-in-virtualbox/QQ20180903-224520@2x.png)
#### 完成安装
点击面板上的`启动`按钮
![](/images/install-centos7-in-virtualbox/QQ20180903-231040@2x.png)
设置ROOT密码/存储位置等，完成配置，重启
![](/images/install-centos7-in-virtualbox/QQ20180909-214025@2x.png)
#### 配置网络
首先要获取宿主机的IP/网关/DNS
``` shell
# windows
$ ipconfig

# osx/linux
$ ifconfig
```
##### 设置网络为桥连网络
关机
``` shell
$ shutdown -h now
```
打开虚拟机所对应的设置，配置好桥连网络并启动
![](/images/install-centos7-in-virtualbox/WX20180909-210529@2x.png)
##### 配置静态IP/DNS
``` shell
$ vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```
配置如下：
``` properties
HWADDR=08:00:27:8D:90:F4
TYPE=Ethernet
BOOTPROTO=static        #静态IP
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
NAME=enp0s3
UUID=970a682c-0a14-4a74-a679-6cf19a09df85
ONBOOT=yes              #开机启动

IPADDR0=192.168.31.180  #固定IP，这里要注意和其他电脑不要冲突
GATEWAY0=192.168.31.1   #网关
DNS1=192.168.31.1       #跟“宿主机”的DNS服务器一样
DNS2=8.8.8.8            #使用谷歌的DNS服务器
```
配置完成后重启网卡
``` shell
$ /etc/init.d/network restart
```
能ping通`$ ping www.baidu.com`即配置成功
##### 安装SSH
安装`openssh-server`
``` shell
$ yum install -y openssh-server
```
##### 测试
宿主机用SSH进行连接
``` shell
$ ssh root@192.168.31.180
```
启动正常，且每次启动都会是相同的IP了
##### 关闭防火墙
如果在虚拟机中部署了网站，要在宿主机上访问，这就需要关闭虚拟机的防火墙或者开放端口
``` shell
# 查看防火墙状态
$ firewall-cmd --state

# 重启防火墙
$ firewall-cmd --reload

# 关闭防火墙
$ systemctl stop firewalld.service      #停止firewall
$ systemctl disable firewalld.service   #禁止firewall开机启动
```
至此，虚拟机安装的教程就到这里了，如果在安装过程中有什么疑问，可直接在下面评论