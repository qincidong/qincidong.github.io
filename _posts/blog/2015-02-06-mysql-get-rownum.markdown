---
layout: post
title:  mysql实现行号的方法——如何获取当前记录所在行号
description: mysql实现行号的方法——如何获取当前记录所在行号
date:   2015-02-06 20:11:39
categories: blog
tags: mysql rownum 行号
---
MYSQL目前不支持行号功能，如果想按某字段进行排序，然后得到排序号，很麻烦，要想实现这种功能，网上的答案五花八门，经过几次实验，得出如下一条SQL文就能简单实现此功能，现共享一下。

