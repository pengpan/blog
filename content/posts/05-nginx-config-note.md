---
title: Nginx常用配置笔记
author: MasterPan
tags:
  - nginx
categories:
  - nginx
abbrlink: 27782
date: 2018-09-15 13:00:00
---
#### 防止恶意解析
```
server {
  listen 80 default_server;
  server_name _;
  return 403;
}
server {
  listen 443 default_server;
  server_name _;
  ssl on;
  ssl_certificate        cert/fullchain.pem;
  ssl_certificate_key    cert/privkey.pem;
  return 403;
}
```
#### 开启压缩
```
gzip on;
gzip_min_length  1k;
gzip_buffers     4 16k;
gzip_http_version 1.1;
gzip_comp_level 5;
gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml image/jpeg image/gif image/png;
gzip_vary on;
gzip_proxied   expired no-cache no-store private auth;
gzip_disable   "MSIE [1-6]\.";
```

#### 去掉www
```
if ($host ~ '^www.9527.blog'){
  return 301 https://9527.blog$request_uri;
}
```

#### 强制https
```
if ($server_port !~ 443){
    rewrite ^(/.*)$ https://$host$1 permanent;
}
```

#### 静态资源配置
```
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
{
    expires      30d;
    error_log off;
    access_log off;
}

location ~ .*\.(js|css)?$
{
    expires      12h;
    error_log off;
    access_log off; 
}
```

#### 防盗链配置
```
location ~ .*\.(jpg|jpeg|gif|png|js|css)$
{
    expires      30d;
    access_log off;
    valid_referers none blocked 9527.blog www.9527.blog;
    if ($invalid_referer){
       rewrite ^/ https://9527.blog;
       #return 404;
    }
}
```

#### 禁止访问的文件或目录
```
location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
{
    return 404;
}
```

#### 文件服务器
```
location ~ .*\.(exe|dmg|zip|apk)$ {
    root /www/wwwroot/files;
    expires -1;
}
```

#### 流量控制
```
limit_conn perserver 500;   #限制当前站点最大并发数
limit_conn perip 10;        #限制单个IP访问最大并发数
limit_rate 2048k;           #限制每个请求的流量上限（单位：KB）
```

本文为经验总结，如有不详之处，请参阅文档
[官方文档](http://nginx.org/en/docs/)
[Nginx中文文档](http://www.nginx.cn/doc/)
[Nginx官方文档中文版](https://tengine.taobao.org/nginx_docs/cn/docs/)

最后，推荐大家一个网站[nginxconfig.io](https://nginxconfig.io/)，上面有很多配置好了的nginx模板
