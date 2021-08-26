---
title: Hexo实现远程VPS自动部署
author: MasterPan
tags:
  - hexo
  - deploy
categories:
  - hexo
abbrlink: 63870
date: 2018-09-02 17:14:00
---
### 前言
Hexo[官方文档](https://hexo.io/zh-cn/docs/deployment)只是粗略的描述了一下，并且不支持自动部署，我的VPS上面是搭建了一个静态服务器，也就是将generate的public文件夹上传至网站目录，这样做的好处是网站是静态的，改了任何东西不需要重启，覆盖文件即可，但是每次改了东西要上传上去，麻烦，何不利用Hexo的deploy命令实现自动部署呢？

### 正文
#### 实现SSH免密登陆
在开发环境生成利用`ssh-keygen`生成公钥/私钥对
打开Windows PowerShell或者terminal(Mac)，执行生成命令
``` shell
$ ssh-keygen -t rsa
```
一路回车即可，公钥位置:`C:\Users\xxx\.ssh\id_rsa.pub`

在VPS上面创建`authorized_keys`文件
``` shell
$ mkdir /root/.ssh && chmod 700 /root/.ssh
$ touch /root/.ssh/authorized_keys && chmod 600 /root/.ssh/authorized_keys
```
然后将本地的`id_rsa.pub`内容粘贴到`authorized_keys`中
或者
```
$ cat ~/.ssh/id_rsa.pub | ssh root@x.x.x.x 'cat >> .ssh/authorized_keys'
```

本地测试是否可以免密登录
``` shell
$ ssh -v root@x.x.x.x
```
#### VPS配置
VPS上面需要创建git裸仓库`blog.git`

blog.git作为远程Git仓库，Hexo在本地生成的博客静态文件可以通过push与其同步
``` shell
$ mkdir /root/blog.git && cd /root/blog.git
$ git init --bare
```
配置Hooks脚本

post-receive脚本将在blog.git仓库接收到push时执行
``` shell
$ vi /root/blog.git/hooks/post-receive
```
将一下内容粘贴进去
```
#!/bin/bash
rm -rf /var/www/blog
git clone /root/blog.git /var/www/blog
```
注意：`/var/www/blog`是网站静态资源的文件夹，也就是本地generate后的public文件夹替换的

给post-receive脚本执行权限
``` shell
$ chmod +x /root/blog.git/hooks/post-receive
```
#### 配置Hexo自动部署
开发环境安装 hexo-deployer-git
``` shell
$ npm install hexo-deployer-git --save
```
配置_config.yml文件
``` yml
deploy:
  type: git
  repo: root@88.88.88.88:blog.git
```
#### 测试
``` shell
$ hexo clean && hero deploy
```
刷新网页看效果。

参考文章：
[https://blog.fundebug.com/2017/05/18/deploy-hexo-on-cloud/](https://blog.fundebug.com/2017/05/18/deploy-hexo-on-cloud/)
[http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/](http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/)