---
layout: post
title:  mysql实现行号的方法——如何获取当前记录所在行号
description: mysql实现行号的方法——如何获取当前记录所在行号
date:   2015-02-06 20:11:39
categories: blog
tags: mysql rownum 行号
---
MYSQL目前不支持行号功能，如果想按某字段进行排序，然后得到排序号，很麻烦，要想实现这种功能，网上的答案五花八门，经过几次实验，得出如下一条SQL文就能简单实现此功能，现共享一下。

表 a：  
UID       Money
2	       444
1	       222
3	       555
4	       6666

想要以Money排序取得排行号：SQL文如下：
<pre>
Select UID,(@rowNum:=@rowNum+1) as rowNo
From a,
(Select (@rowNum :=0) ) b
Order by a.Money Desc
</pre>

输入结果如下：  
UID	       rowNo
4	       1
3	       2
2	       3
1	       4

转载：[http://www.cnblogs.com/xinlei/archive/2011/12/16/2290349.html](http://www.cnblogs.com/xinlei/archive/2011/12/16/2290349.html)
