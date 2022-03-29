---
title: CentOS 安装ali yum 源
date: 2022-03-26T23:15:39+08:00
draft: true
tags: 
- CentOS
categories:
- CentOS
---

centos8 修改yum源为阿里源,某下网络下速度比较快 首先是到yum源设置文件夹里
```bash

#安装base reop源
cd /etc/yum.repos.d
#接着备份旧的配置文件

sudo mv CentOS-Base.repo CentOS-Base.repo.bak
#下载阿里源的文件

sudo wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
#安装epel repo源
#epel(RHEL 7)

sudo wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
#清理缓存
yum clean all
#重新生成缓存
yum makecache
```
