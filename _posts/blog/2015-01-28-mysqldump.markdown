---
layout: post
title:  Mysql导出(多张表)表结构及表数据 mysqldump用法
description: Mysql导出(多张表)表结构及表数据 mysqldump用法
date:   2015-01-28 18:15:39
categories: blog
tags: mysql mysqldump
---
命令行下具体用法如下：  mysqldump -u用戶名 -p密码 -d 數據库名 表名 脚本名;

1、导出數據库為dbname的表结构（其中用戶名為root,密码為dbpasswd,生成的脚本名為db.sql）
mysqldump -uroot -pdbpasswd -d dbname >db.sql;

2、导出數據库為dbname某张表(test)结构
mysqldump -uroot -pdbpasswd -d dbname test>db.sql;

3、导出數據库為dbname所有表结构及表數據（不加-d）
mysqldump -uroot -pdbpasswd  dbname >db.sql;

4、导出數據库為dbname某张表(test)结构及表數據（不加-d）
mysqldump -uroot -pdbpasswd dbname test>db.sql;

5.导出数据中dbname多张表（test1,test2,test3）结构及表数据用用空格隔开

mysqldump -uroot -pdbpasswd dbname test1 test2 test3>db.sql;

实例（导出t_buyer,t_address,t_orders,t_payment,t_deliver5张表的结构和数据）：
C:\Program Files (x86)\MySQL\MySQL Server 5.0\bin>mysqldump -uroot -p123456 -h localhost cimecom t_buyer t_address t_orders t_deli
ver t_payment > d:/cimecom_gls_order_init.sql
