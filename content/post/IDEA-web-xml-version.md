+++
title = "修改 IDEA Maven WebApp web.xml 的版本"
date = "2018-07-25 08:55:36"
tags = ["IDEA"]
categories = ["IDE"]

+++

## 引言

记录修改 IDEA Maven WebApp `web.xml` 的过程。

目前，使用IDEA创建Maven webapp项目默认生成的web.xml文件的版本是2.3，文件内容如下：

<!--more-->

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>
</web-app>
```

## 为什么要去修改web.xml的版本？

实际上，这个问题是在使用JSP模板进行开发时所引出的，如果不使用JSP模板或者JSP中没有使用EL表达式(使用JSP的话，应该都会用到😰)，不会出现什么问题。但是如果有使用的话，在2.3版本web.xml的环境中，EL表达式是默认不生效的。可能是因为JSP模板已经落后时代，所以IDEA对这方面没有进行更新。另外，如何在当前版本让EL表达式生效，可以在JSP中添加如下内容：

```jsp
<%@ page isELIgnored="false" %>
```

这个方法需要在每个JSP中添加，比较麻烦。那么是否可以将web.xml版本升级，以便支持EL表达式。

> The default value of `isELIgnored` varies depending on the version of the web application deployment descriptor. The default mode for JSP pages delivered with a Servlet 2.4 descriptor is to evaluate EL expressions; this automatically provides the default that most applications want. The default mode for JSP pages delivered using a descriptor from Servlet 2.3 or before is to ignore EL expressions; this provides backward compatibility. 

根据Oracle[文档](https://docs.oracle.com/cd/E19316-01/819-3669/bnaic/index.html)说明，新的版本默认支持EL表达式。

## 如何修改

1. 在你的maven本地仓库中，根据如下坐标找到jar包:
    ```java
    org.apache.maven.archetypes.maven-archetype-webapp.1.3
    ```

2. 用解压工具打开`maven-archetype-webapp-1.3.jar`包（无需解压），可以发现里面包含一个`web.xml`，文件内容为：新建一个`web.xml`文件，内容如下：

    ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
            version="3.1">
      
     <display-name>Archetype Created Web Application</display-name>
      </web-app>
    ```

    然后，用这个文件将jar包的web.xml替换，即可。

3. 使用IDEA创建一个Maven webapp项目，即可发现web.xml的版本已经修改成功。