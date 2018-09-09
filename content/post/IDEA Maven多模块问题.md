---
title: IDEA Maven多模块问题
date: 2018-07-29 10:21:00
tags:
- IDEA
- Maven
categories:
- IDE
---

使用IDEA进行Maven多模块项目开发时，出现parent项目中统一管理的jar在子模块引用时，并没有导入，查找发现，子模块的`pom.xml`被忽略。

通过`File | Settings | Build, Execution, Deployment | Build Tools | Maven | Ignored Files`找到忽略的`pom.xml`，将勾选取消即可。