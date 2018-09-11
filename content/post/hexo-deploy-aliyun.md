---
title: "阿里云ECS搭建Hexo"
date: 2018-09-10
tags: 
- hexo
- aliyun
categories: 
- 工具
author: "walker"
---

使用阿里云 ECS 部署博客 Hexo，主要记录 Hexo 如何 push 到阿里云服务器，实现本地环境 Hexo 生成静态文件，将文件 push 到服务器后，通过 git-hooks 自动将文件 checkout 到部署目录。

## 环境

- 服务器：阿里云2核4G
- 系统：CentOS7
- nginx：借助 LNMP 所安装的 nginx

<!--more-->

## 搭建过程

![hexo](https://walker911-1256994604.cos.ap-shanghai.myqcloud.com/blog%2Fhexo.png)

这里主要是按照上图所示内容进行部署。

### Hexo 本地环境

Hexo 本地环境非常容易进行搭建，这里不进行详细说明。本地环境主要是用来写文章以及将文章生成为静态文件。

### Git 环境

1. 在服务器上安装 git

2. 将本地`~/.ssh/id_rsa.pub`中的公钥，复制粘贴到服务器`~/.ssh/authorized_keys`文件中

   ps: 如果服务器上没有`~/.ssh`和`~/.ssh/authorized_keys`，可以进行创建

3. 给``~/.ssh/authorized_keys`文件添加权限

   ```bash
   chmod +x authorized_keys
   ```

4. 在本地执行`ssh`命令测试是否可以免密登录

   ```bash
   # ip: 阿里云公网ip
   ssh -v git@ip
   ```

### 自动化部署

1. 在服务器上建立 git 裸库

   ```bash
   cd /home
   git init --bare hexo.git
   ```

2. 使用 git-hooks 同步文件

   ```bash
   cd hexo.git
   vim ./post-receive
   ```

   post-receive 文件内容：

   ```bash
   !/bin/sh
   git --work-tree=/home/wwwroot/default/hexo --git-dir=/home/hexo.git checkout -f
   ```

   > 显示指定 git-dir (.git目录) 和 work-tree, -f 强制 checkout

   给文件添加权限

   ```bash
   chmod +x post-receive
   ```

3. 本地 Hexo，配置`_config.yml`

   修改内容：

   ```yaml
   # ip-公网ip
   deploy:
     type: git
     repository: root@ip:/home/hexo.git
     branch: master
     message: update
   ```

## 测试

1. 生成md文件，并加入测试内容

2. 执行命令，然后访问，测试文章是否发布成功

   ```bash
   hexo clean & hexo g -d
   ```


