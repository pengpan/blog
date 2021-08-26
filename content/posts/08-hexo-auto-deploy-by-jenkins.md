---
title: Hexo通过jenkins实现自动部署
author: MasterPan
tags:
  - hexo
  - deploy
  - jenkins
categories:
  - hexo
abbrlink: 10289
date: 2018-10-15 23:57:00
---

### 前言
之前有写过一篇[Hexo实现远程VPS自动部署](/posts/63870.html)，其实这并不是真正意义上的自动部署，需要执行`hexo deploy`，这里我们用jenkins和github来实现真正的自动部署，只需要提交代码到github仓库即自动构建发布。

### 正文

#### 安装jenkins
首先得先安装jenkins，这里不作讲述

#### 创建jenkins任务
![](/images/hexo-auto-deploy-by-jenkins/QQ20181015-224718@2x.png)
填写源码地址
![](/images/hexo-auto-deploy-by-jenkins/QQ20181015-224901@2x.png)
配置触发和构建脚本
![](/images/hexo-auto-deploy-by-jenkins/QQ20181016-004606@2x.png)
shell 脚本
``` shell
#!/bin/bash -ilex

yarn install
hexo clean && hexo generate

rm -rf /www/wwwroot/9527.blog/*
cp -rf public/* /www/wwwroot/9527.blog/
```
脚本的含义是先将github仓库中拉取的代码编译成静态html，然后再替换到站点目录
#### github配置
添加webhook
![](/images/hexo-auto-deploy-by-jenkins/QQ20181015-230229@2x.png)
Payload URL为jenkins地址加上`/github-webhook/`，最后的那根`/`不能丢
![](/images/hexo-auto-deploy-by-jenkins/QQ20181015-234700@2x.png)
#### 配置完成
修改github仓库代码，自动触发构建
![](/images/hexo-auto-deploy-by-jenkins/QQ20181015-235031@2x.png)
