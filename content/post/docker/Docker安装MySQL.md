---
title: Docker安装Mysql

date: #文章日期格式：2018-05-30 15:20:36

tags: 
- docker
---

一、下载
下载镜像
```bash
docker pull mysql:8.0.16
```

将全部的配置文件和关联的文件夹统一放到/opt/docker-mysql中

二、配置文件
```bash
mkdir -p /opt/docker-mysql/conf.d
```
增加并修改配置文件config-file.cnf

内容如下,设置表名不区分大小写; linux下默认是区分的，windows下默认不区分

```properties
sudo tee /opt/docker-mysql/conf.d/config-file.cnf <<-'EOF'
[mysqld]
# 表名不区分大小写
lower_case_table_names=1 
#server-id=1
datadir=/var/lib/mysql
#socket=/var/lib/mysql/mysqlx.sock
#symbolic-links=0
# sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
EOF
```

三、启动
增加数据文件夹
```bash
mkdir -p /opt/docker-mysql/var/lib/mysql
```
启动，设置默认密码 123456-abc
```bash
docker run --name mysql \
    --restart=always \
    -p 3306:3306 \
    -v /opt/docker-mysql/conf.d:/etc/mysql/conf.d \
    -v /opt/docker-mysql/var/lib/mysql:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=123456-abc \
    -d mysql:8.0.16
```
四、 常用命令
进入容器
```bash
docker exec -it mysql bash
```
查看日志
```bash
docker logs -f mysql
```
备份数据
```bash
docker exec mysql sh -c 'exec mysqldump --all-databases -uroot -p"123456-abc"' > /some/path/on/your/host/all-databases.sql
```
恢复数据
```bash
docker exec -i mysql sh -c 'exec mysql -uroot -p"123456-abc"' < /some/path/on/your/host/all-databases.sql
```
五、 其他问题
only_full_group_by 问题
如果安装的版本是 5.7版本, 查询数据时出现如下错误

this is incompatible with sql_mode=only_full_group_by

可以使用使用下列方式解决

1. 查询 sql_mode
select @@sql_mode
结果如下

ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

2. 重置
删除其中的 ONLY_FULL_GROUP_BY配置，重新设置到 config-file.cnf中
```properties

[mysqld]
# 表名不区分大小写
lower_case_table_names=1 
#server-id=1
datadir=/var/lib/mysql
#socket=/var/lib/mysql/mysqlx.sock
#symbolic-links=0
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
3. 重启容器
```bash
docker restart mysql
```

作者：QuincyZ
链接：https://www.jianshu.com/p/d6febf6f95e0
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。