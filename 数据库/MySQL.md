# 基础篇

## 一 入门介绍

### 概述

###### MySQL安装方式

* 软件仓库(YUM或APT)
* RPM或DEB文件
* 通用二进制文件
* Docker

###### 登录MySQL服务器的方式

mysql -h <ip> -P <port> -u <username> -p<password>

> 1. -P参数用于指定端口
> 2. -p参数用户指定密码,==且没有空格==

###### 忘记密码如何重置

###### RDBMS是什么

关系数据库管理系统

###### MySQL操作分类

* DML(Data Manage Language)

 数据操作语言,包括

* DDL
* DCL
* DML

## 数据类型

### 整数型



### 浮点数

### 字符串

### JSON

## SQL语法

### 不常用语法



### 数据库

```mys
# 要查找连接到了哪个数据库
SELECT DATABASE() ;

# 切换数据库

# 查询所有数据库
SHOW DATABASES ;

```



```mysql
INSERT IGNORE INTO customers(first_name,last_name,country)
VALUES
（Mike ’，’ Chr 工 stensen ’，『 USA 『 ） ，
（Andy 『，’ Hollands ’,’ Australia ’ ) ,
(Ravi ’,’ Vedantam ?, ’ I ndia ’ ) ,
( ’ Ra ] iv ’ , ’ Perera ’,’ s ri Lanka ' ) ;
```









### 数据表

### 插入

### 修改

### 删除

### 查询



## 三  



# 中级篇



# 高级篇

