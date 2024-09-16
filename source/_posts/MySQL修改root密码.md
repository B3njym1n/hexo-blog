---
title: MySQL修改root密码
date: 2024-09-12 13:43:38
tags:
---


# Table of Contents

1.  [MySQL修改root密码](#org7a1b2ef)
    1.  [login MySQL](#org74c6a81)
    2.  [relogin](#org0cc3606)


<a id="org7a1b2ef"></a>

# MySQL修改root密码


<a id="org74c6a81"></a>

## login MySQL

    mysql -u root -p

    mysql> update user set password=password('newpassword') where user='root';
    
    mysql> FLUSH PRIVILEGES;
    
    mysql> quit


<a id="org0cc3606"></a>

## relogin
