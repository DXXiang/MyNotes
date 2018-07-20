# MySQL相关
##安装
先做安装准备
**sudo apt update**
**sudo apt upgrade**


然后安装
**sudo apt install mysql**
途中会让你设置root密码
## 登录数据库
**mysql -u root -p**
然后让你输入密码
## 错误解决
* 使用可视化工具连接rook出现 **Authentication plugin 'caching_sha2_password' cannot be loaded: /usr/lib/mysql/plugin/caching_sha2_....没有那个文件**的解决方式
> **use user;**
**ALTER USER root@localhost  IDENTIFIED WITH mysql_native_password BY 'root密码';**

## 概述
###默认端口：3306
###超级用户：root
## DCL
###创建用户
* 用户只能在指定的IP地址登录
> create user 用户名@ip地址 IDANTIFIED BY "密码";
* 用户可以在任何ip地址登录
>create user 用户名@'%' IDANTIFIED BY "密码";
###查看权限
* SHOW GRANTS FOR 用户名@ip地址
### 授权
* GRANT 权限1，权限2，权限n ON 数据库.* TO 用户名@ip地址
* GRANT ALL ON 数据库.* TO 用户名@ip地址
### 解除授权
* REVOKE 权限1，权限2，权限n ON 数据库.* FROM 用户名@ip地址
* REVOKE ALL ON 数据库.* FROM 用户名@ip地址
##编码
###查看编码
* SHOW VARIABLES LIKE 'char%';
###修改
* set 编码名=编码值
##备份与恢复
###1.使用命令备份
1. 数据库导出SQL脚本基本语法
> **mysqldump -u 用户名 -p 数据库名** [表名1 表名2 ... 表名N]** \>/路径/备份文件名.sql**
2. 备份多个数据库加上--databases选项
> **mysqldump -u 用户名 -p --databases 数据库名1 数据库名2 \>/路径/备份文件名.sql**
2. 备份所有数据库加上-all-databases选项
> **mysqldump -u 用户名 -p -all-databases  \>/路径/备份文件名.sql**
###2.直接复制整个数据库目录
###3.使用mysqlhotcopy工具快速备份（热备份）
* 语法
> mysqlhotcopy [option] dbname1 dbname2 backupDir/

###4.数据还原
1. 语法如下
> mysql -u root -p [dbname] < backup.sql
2. 还原直接复制目录的备份
>通过这种方式还原时，必须保证两个MySQL数据库的版本号是相同的。MyISAM类型的表有效，对于InnoDB类型的表不可用，InnoDB表的表空间不能直接复制。