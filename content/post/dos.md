---
title: cmd 命令使用清单
date: 2018-11-6
categories: [工具]
tags: [cmd]
---

记录一些使用的 `cmd` 命令清单。

## 命令

- 终止进程：`taskkill /f /im chrome.exe `
- tasklist：任务列表

## 遇到的问题

遇到 windows 端口被占用，又不想去任务管理器中逐个查找，可以使用下述过程进行处理，这种情况是 hexo 和 福昕阅读器端口出现冲突的情况。

1. 查看端口占用的进程ID(`PID`)

   ```powershell
   netstat -ano  各个端口占用和进程PID
   // 查看4000端口情况
   netstat -ano | findstr "4000"
   ```

2. 查看端口号所对应的应用程序

   ```powershell
   tasklist | findstr "4632"
   ```

3. 终止进程

   ```powershell
   taskkill /pid 4632 /F
   or
   taskkill /f /t /im 进程
   ```


