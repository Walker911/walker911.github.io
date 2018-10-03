---
title: Maven安装和配置
date: 2018-06-06 23:21:35
categories: [Maven]
tags: [Maven]
---

## 环境

- Windows 10 专业版

## 安装和配置

### 下载

在maven[官网](https://maven.apache.org/)下载maven的免安装`zip`包，下载完成后解压到指定目录，例如：

```powershell
D:\build-tools\maven\apache-maven-3.5.3
```

<!--more-->

### 配置Maven环境变量

#### 环境变量

1. 在Windows环境变量中，配置系统变量名`MAVEN_HOME`，变量值`D:\build-tools\maven\apache-maven-3.5.3`
2. 编辑`PATH`环境变量，在`Path`中添加`%MAVEN_HOME%\bin`，点击确定即完成

#### 检查是否配置成功

- 打开`CMD`窗口，利用命令`mvn -v`测试是否配置成功

    ```powershell
    # 安装成功结果
    Apache Maven 3.5.3 (3383c37e1f9e9b3bc3df5050c29c8aff9f295297; 2018-02-25T03:49:05+08:00)
    Maven home: D:\build-tools\maven\apache-maven-3.5.3\bin\..
    Java version: 1.8.0_172, vendor: Oracle Corporation
    Java home: C:\Program Files\Java\jdk1.8.0_172\jre
    Default locale: zh_CN, platform encoding: GBK
    OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
    ```

### 配置`JDK1.8`环境

- 全局配置JDK，在maven的`setting.xml`文件中添加如下代码：

    ```xml
    <profile>    
    	<id>jdk18</id>    
      <activation>    
      	<activeByDefault>true</activeByDefault>    
          <jdk>1.8</jdk>    
      </activation>    
      <properties>    
          <maven.compiler.source>1.8</maven.compiler.source>    
          <maven.compiler.target>1.8</maven.compiler.target>    
          <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>    
      </properties>     
    </profile>
    ```

## Maven镜像仓库配置

- maven阿里云镜像仓库配置，在maven的`setting.xml`中添加：

    ```xml
<mirror>  
	  <id>alimaven</id>  
	  <name>aliyun maven</name>  
	  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
	  <mirrorOf>central</mirrorOf>          
</mirror> 
    ```

## IDEA集成Maven

#### 使用IDEA自带Maven

IDEA自身已经集成了Maven，但是使用的是Maven中央仓库，由于网络限制原因，下载`jar`包比较慢，所以在默认配置路径`~\.m2`中放入配置好镜像仓库的`setting.xml`，然后`override`，即可修改镜像仓库

#### 使用安装的Maven

使用自己配置的Maven，只需选择自己安装的Maven版本，并`override`配置文件`setting.xml`的路径，即可