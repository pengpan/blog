---
title: 利用chevereto在VPS上搭建私有图床
author: MasterPan
tags:
  - chevereto
categories:
  - tools
abbrlink: 10741
date: 2018-12-02 19:30:00
---

### 前言
除了使用公共的图床，也可以使用VPS去搭建自己的个人图床。自建图床可以存储自己收藏的图片，也可以创建一些加密的相册，这次要给你介绍的是 [Chevereto](https://chevereto.com)这款超高颜值的图床程序。它可以非常简便的上传图片（支持多图上传）并自动生成代码链接供其他程序引用，支持使用外部储存，开放用户注册上传等。

### 正文
#### 传统方式搭建
传统方式比较繁琐，这里推荐[biezhi](https://github.com/biezhi)的教程[自建图床chevereto](https://gist.github.com/biezhi/f90923b48863c7d745481ccdd678ccab)


#### 使用Docker搭建
##### 首先需要准备[mysql](https://hub.docker.com/r/library/mysql/)环境
``` shell
$ docker run \
    --name mysql \
    -e MYSQL_ROOT_PASSWORD=my-secret-pw \
    -e MYSQL_DATABASE=chevereto \
    -e MYSQL_USER=chevereto \
    -e MYSQL_PASSWORD=chevereto \
    -d mysql:5.7 \
    --character-set-server=utf8mb4 \
    --collation-server=utf8mb4_unicode_ci
```

##### 然后运行[chevereto](https://hub.docker.com/r/nmtan/chevereto/)官方镜像
``` shell
$ docker run -it --name chevereto -d \
    --link mysql:mysql \
    -p 80:80 \
    -v "$PWD/images":/var/www/html/images \
    -e "CHEVERETO_DB_HOST=$(docker ps -f "name=mysql" -aq)" \
    -e "CHEVERETO_DB_USERNAME=chevereto" \
    -e "CHEVERETO_DB_PASSWORD=chevereto" \
    -e "CHEVERETO_DB_NAME=chevereto" \
    -e "CHEVERETO_DB_PREFIX=chv_" \
    nmtan/chevereto
```
##### 小插曲
访问`http://localhost`出现系统错误`No write permission in /images/ directory. Chevereto needs to be able to write in this directory.`
![](/images/build-private-graph-bed/20181203122342.png)
`/images/`目录中没有写权限，解决方案:
1. 进入chevereto容器`docker exec -it $(docker ps -f "name=chevereto" -aq) /bin/bash`
2. 赋予`/images/`目录写入权限`chmod -R 222 /var/www/html/images/`
3. 退出容器`exit`
刷新网页，填写管理员信息即可使用
![](/images/build-private-graph-bed/20181203122920.png)
