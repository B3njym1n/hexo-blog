---
title: MySQL添加用户、删除用户与授权
date: 2024-09-12 14:27:02
tags:
---


1. add new user:

	     mysql> insert into mysql.user(Host, User, Passwd) values("localhost", "test", "1234"))
     
    > 注意：此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录


2. Authoriz new user

	授权格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";

	1. 登录MYSQL（有ROOT权限），这里以ROOT身份登录：

	2. 首先为用户创建一个数据库(testDB)：
	
            mysql>create database testDB;

	4. 授权test用户拥有 testDB数据库的所有权限（某个数据库的所有权限）：
	
            mysql>grant all privileges on testDB.* to test@localhost identified by '1234';
            mysql>flush privileges;//刷新系统权限表
	    
	5. 如果想指定 部分权限给一用户，可以这样来写:	
	
            mysql>grant select,update on testDB.\* to test@localhost identified by '1234';
            mysql>flush privileges; //刷新系统权限表
	    
	6. 授权test用户拥有 所有数据库的某些权限:
	
            mysql>grant select,delete,update,create,drop on *.* to test @"%" identified by "1234"; //test用户对所有数据库都有select,delete,update,create,drop 权限。
            // @"%" 表示对所有非本地主机授权，不包括 localhost。（localhost 地址设为 127.0.0.1，如果设为真实的本地地址，不知道是否可以，没有验证。）
            //对 localhost 授权：加上一句 grant all privileges on testDB.\* to test@localhost identified by '1234';即可。

3. delete USER

            mysql>Delete FROM user Where User='test' and Host='localhost';
            mysql>flush privileges;
            mysql>drop database testDB; //删除用户的数据库
        删除账户及权限：
            drop user 用户名@'%';
            drop user 用户名@ localhost;

4. modify user password

	    mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";
	    mysql>flush privileges;

5. 切换数据库

	    mysql>use '数据库名';

6. 列出所有表

	    mysql>show tables;

7. 显示数据表结构

	    mysql>describe 表名;

8. 删除数据库和数据表

	    mysql>drop database 数据库名;
	    mysql>drop table 数据表名;
