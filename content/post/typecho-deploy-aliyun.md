---
title: "阿里云ECS搭建Typecho"
date: 2018-09-09T15:36:00+08:00
tags: 
- typecho
- aliyun
categories: 
- 工具
author: "walker"
---

使用阿里云 ECS 搭建博客 [Typecho](http://typecho.org/)，记录下搭建流程和遇到的问题。

<!--more-->

## 环境

- 服务器：阿里云2核4G
- 系统：CentOS7
- Typecho：1.1
- LNMP：1.5

## 搭建过程

主要介绍 LNMP，Typecho 的安装过程。

### LNMP 安装

简单介绍LNMP安装，详细可参考 [LNMP](https://lnmp.org/install.html) 提供的安装过程。

1. 下载 LNMP 安装包并安装

    ```bash
    # 获取安装包
    wget http://soft.vpser.net/lnmp/lnmp1.5.tar.gz
    # 解压
    tar zxf lnmp1.5.tar.gz 
    # 安装
    cd lnmp1.5
    ./install.sh lnmp
    ```

    根据提示选择需要安装的版本，这里基本上都是选择默认设置进行安装，然后等待安装过程的执行，当出现安装成功的界面，说明已经安装完成。

2. 测试是否安装成功

    因为 nginx 默认是监听80端口，所以在测试之前，我们需要在云服务器实例安全组配置规则，将80端口授权可以访问。成功配置规则后，访问实例的公网 IP, 会看到 LNMP 提供的首页，即说明安装成功。

### Typecho 安装

1. 下载 typecho [安装包](http://typecho.org/download)，然后上传到服务器，也可以在服务器上执行下面的命令进行获取。

    ```bash
    wget http://typecho.org/downloads/1.1-17.10.30-release.tar.gz
    ```

2. 将安装包解压到指定目录，这里由于 LNMP 安装后，nginx 默认 root 指向为`/home/wwwroot/default`，我们这里在这个目录下新建`blog`目录，然后将 typecho 解压到这个目录。

3. 访问`ip(公网ip)/blog/index.php`，根据提示进行安装

    **注意**：安装前在 MySQL 中建立`typecho`数据库

4. 安装完成，访问`ip/blog`测试是否安装成功

## 遇到的问题

- 登录跳转失败，报404错误

    nginx 伪静态化问题，找到`nginx.conf`配置文件，这里按如下修改，解决了该问题，

    ```bash
    # 修改前
    server {
      include enable-php.conf;
    }
    # 修改为
    server {
      include enable-php-pathinfo.conf;
    }
    ```

- Navicat 远程连接阿里云 MySQL 报10060错误

    1. 安全组放开3306端口

    2. `mysql`数据中`user`表设置`root`的`host`为`%`

        ```mysql
        update user set host='%' where user='root';
        ```

    3. 重启 MySQL 服务

        ```bash
        service mysql restart
        ```

    4. 重启实例

## 总结

初步搭建 Typecho，不涉及域名方面的配置，暂时使用 IP 进行访问，后续使用域名

