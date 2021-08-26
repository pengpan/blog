---
title: Docker搭建Mysql PXC集群
author: MasterPan
tags:
  - mysql
  - pxc
  - replication
categories:
  - tools
abbrlink: 16193
date: 2019-01-04 14:14:00
---

### 前言
> Mysql集群方案

 - replication：速度快、弱一致性、数据同步单向，异步复制
 - pxc：速度慢、强一致性、数据同步双向，同步复制

### 正文

#### 如何创建MySQL的PXC集群
1. 下载PXC集群镜像文件
``` shell
# 下载
$ docker pull percona/percona-xtradb-cluster

# 重命名
$ docker tag docker.io/percona/percona-xtradb-cluster pxc
```

2. 创建内部网络
处于安全考虑，需要给PXC集群实例创建Docker内部网络
``` shell
$ docker network create --subnet=172.96.0.0/24 net1
```

3. 查看网段
``` shell
$ docker network inspect net1
```

4. 创建Docker卷
--name：设置卷名，可以不写
``` shell
$ docker volume create --name v1
$ docker volume create --name v2
$ docker volume create --name v3
$ docker volume create --name v4
$ docker volume create --name v5
```

5. 创建PXC容器
``` shell
$ docker run \
-p 3306:3306 \
-v v1:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=abc123456 \
-e CLUSTER_NAME=PXC \
-e XTRABACKUP_PASSWORD=abc123456 \
--privileged \
--name=node1 \
--net=net1 \
--ip 172.96.0.2 \
-d pxc

$ docker run \
-p 3307:3306 \
-v v2:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=abc123456 \
-e CLUSTER_NAME=PXC \
-e XTRABACKUP_PASSWORD=abc123456 \
--privileged \
--name=node2 \
--net=net1 \
--ip 172.96.0.3 \
-d pxc

$ docker run \
-p 3308:3306 \
-v v3:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=abc123456 \
-e CLUSTER_NAME=PXC \
-e XTRABACKUP_PASSWORD=abc123456 \
--privileged \
--name=node3 \
--net=net1 \
--ip 172.96.0.4 \
-d pxc

$ docker run \
-p 3309:3306 \
-v v4:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=abc123456 \
-e CLUSTER_NAME=PXC \
-e XTRABACKUP_PASSWORD=abc123456 \
--privileged \
--name=node4 \
--net=net1 \
--ip 172.96.0.5 \
-d pxc

$ docker run \
-p 3310:3306 \
-v v5:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=abc123456 \
-e CLUSTER_NAME=PXC \
-e XTRABACKUP_PASSWORD=abc123456 \
--privileged \
--name=node5 \
--net=net1 \
--ip 172.96.0.6 \
-d pxc

---分割线---

参数解释：
-d：表示后台运行
-v：映射Docker卷到容器的mysql数据目录
MYSQL_ROOT_PASSWORD：表示创建的数据库密码
CLUSTER_NAME：表示创建出的集群名字
XTRABACKUP_PASSWORD：表示集群通信密码
--privileged：表示分配最高权限
--net：指定网段
--ip：指定IP
```

    5个节点创建完成后，可以用MySQL客户端连接进行测试，在任一个mysql节点进行操作，都会同步到其他四个节点

#### 如何创建MySQL的replication集群
1. Start master
``` shell
$ docker run \
 --name mysql_master \
 -p 3311:3306 \
 -v /data/mastermysql:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=mysqlroot \
 -e MYSQL_USER=example_user \
 -e MYSQL_PASSWORD=mysqlpwd \
 -e MYSQL_DATABASE=example \
 -e REPLICATION_USER=replication_user \
 -e REPLICATION_PASSWORD=myreplpassword \
 -d actency/docker-mysql-replication:5.7
```

2. Start slave
``` shell
$ docker run \
 --name mysql_slave \
 -p 3312:3306 \
 -v /data/slavemysql:/var/lib/mysql \
 -e MYSQL_ROOT_PASSWORD=root \
 -e MYSQL_USER=example_user \
 -e MYSQL_PASSWORD=mysqlpwd \
 -e MYSQL_DATABASE=example \
 -e REPLICATION_USER=replication_user \
 -e REPLICATION_PASSWORD=myreplpassword \
 --link mysql_master:master \
 -d actency/docker-mysql-replication:5.7
 ```

3. Check replication status
``` shell
$ docker exec -it mysql_slave mysql -uroot -pmysqlroot -e "SHOW SLAVE STATUS\G;"
```
