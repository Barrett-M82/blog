---
title: windows 清除端口占用
tags:
- windows
categories:
- windows
---

在windows操作系统中，查询端口占用和清除端口占用的程序
```bash
netstat -ano|findstr 8080

taskkill /pid {pid} /F
```
