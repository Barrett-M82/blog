---
title: yum 安装docker

date: #文章日期格式：2018-05-30 15:20:36

tags: 
- docker
---

## CentOS 7使用yum安装Docker
### 1.由于之前没有安装过，直接安装
```bash
cd /etc/yum.repos.d/
sudo wget http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum install -y docker-ce
```
### 2.设置启动项
```bash
systemctl enable docker.service
```
### 3.卸载旧版本
```bash
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
