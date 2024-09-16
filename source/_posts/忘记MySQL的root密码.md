---
title: 忘记MySQL的root密码
date: 2024-09-12 13:19:07
tags:
  - Linux
  - MySQL
categories:
  - Linux
  - MySQL
  - DataBase
---


# Table of Contents

1.  [忘记MySQL的root密码](#org6cd555f)
2.  [编辑MySQL配置文件my.cnf](#orgb8487ab)
3.  [进入MySQL控制台](#orgc8752b8)
4.  [修改root密码](#orgbaa5d9c)
5.  [取消/etc/my.cnf中的skip-grant-tables](#org8b244fd)
6.  [重启mysql](#org1463eab)
7.  [进入mysql控制台](#orgab9ce0c)


<a id="org6cd555f"></a>

# 忘记MySQL的root密码


<a id="orgb8487ab"></a>

# 编辑MySQL配置文件my.cnf

编辑文件，找到[mysqld]，在下面添加一行skip-grant-tables

    vi /etc/my.cnf  

    [mysqld]
    skip-grant-tables

    :wq!

保存退出
重启MySQL服务

    service mysqld restart 


<a id="orgc8752b8"></a>

# 进入MySQL控制台

    mysql -uroot -p


<a id="orgbaa5d9c"></a>

# 修改root密码

    update mysql.user set password=password('123456') where User="root" and Host="localhost";
    
    flush privileges;  #刷新系统授权表
    
    grant all on *.* to 'root'@'localhost' identified by '123456' with grant option;


<a id="org8b244fd"></a>

# 取消/etc/my.cnf中的skip-grant-tables

编辑文件，找到[mysqld]，删除skip-grant-tables这一行

    vi /etc/my.cnf


<a id="org1463eab"></a>

# 重启mysql

    service mysqld restart


<a id="orgab9ce0c"></a>

# 进入mysql控制台

    mysql -uroot -p  #进入mysql控制台

