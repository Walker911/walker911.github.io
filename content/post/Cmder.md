---
title: Cmder介绍
date: 2018-08-10 23:20:00
tags: [cmder]
categories: [工具]
---

`cmder` 一款可以用来替代 Windows 自带 cmd 的工具。

官网：[cmder](http://cmder.net/)，完整版也对`git`进行了集成。

<!--more-->

## 安装

下载后，解压即可使用

## 版本

- version: 1.3.6

## 配置

### 环境变量

此电脑 > 右键属性 > 高级系统设置 > 环境变量 > 系统变量，在`path`中添加cmder路径 

### 中文乱码

在Settings-Startup-Environment-Set up environment variables 里面添加代码：

```shell
set LANG=zh_CN.UTF-8
set LC_ALL=zh_CN.UTF-8
```

## IDEA集成Cmder

IDEA 开发工具支持将默认的`cmd` terminal 代换为 cmder，替换方法如下：

1. 在IDEA选择`File | Settings | Tools | Terminal`

2. 然后在右侧找到`Application settings`中的`Shell path`

3. 将`Shell path`修改为

   ```powershell
   "cmd.exe" /k ""%CMDER_ROOT%\vendor\init.bat""
   ```

   **注意**：

   - 引号不能省略
   - `cmd.exe /k xx`代表执行xx命令后不关闭窗口
   - `%CMDER_ROOR%`为配置的环境变量

