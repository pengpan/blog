---
title: SpringBoot使用jasypt加密配置文件
author: MasterPan
tags:
  - springboot
  - spring
  - jasypt
categories:
  - tools
abbrlink: 20770
date: 2018-09-28 19:57:00
---

### 前言
使用过SpringBoot配置文件的朋友都知道，资源文件中的内容通常情况下是明文显示，安全性就比较低一些。打开application.properties或application.yml，比如mysql登陆密码，redis登陆密码以及第三方的密钥等等一览无余，这里介绍一个加解密组件，提高一些属性配置的安全性。

### 正文
> jasypt由一个国外大神写了一个springboot下的工具包
> 开源地址：[https://github.com/ulisesbocchio/jasypt-spring-boot](https://github.com/ulisesbocchio/jasypt-spring-boot)

#### Maven依赖
```xml
<dependency>
        <groupId>com.github.ulisesbocchio</groupId>
        <artifactId>jasypt-spring-boot-starter</artifactId>
        <version>2.1.0</version>
</dependency>
```

#### 配置加密参数
```yaml
jasypt:
  encryptor:
    password: MasterPan #可以是任意字符
```

### 使用加密
```yaml
datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8
    username: root
    password: ENC(BfsoZM9dAAU4lVp+pE47Y/0N9fqcci1A)
```
注：`ENC(BfsoZM9dAAU4lVp+pE47Y/0N9fqcci1A)`为加密后的密码

### 生成密文
```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {

    @Autowired
    private StringEncryptor stringEncryptor;

    @Test
    public void contextLoads() {
        String password = "123456";
        System.out.println(String.format("ENC(%s)", stringEncryptor.encrypt(password)));
    }

}
```
将生成的密文替换明文即可，简单四步完成加密。