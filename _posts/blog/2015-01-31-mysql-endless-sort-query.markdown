---
layout: post
title:  mysql递归查询
description: mysql递归查询，mysql中从子类ID查询所有父类（做无限分类经常用到）
date:   2015-01-31 15:38:39
categories: blog
tags: mysql
---
由于mysql 不支持类似 oracle with ...connect的 递归查询语法  
之前一直以为类似的查询要么用存储过程要么只能用程序写递归查询.

现在发现原来一条sql语句也是可以搞定的  

先来看数据表的结构如下：

id  name    parent_id   
---------------------------   
1   Home        0   
2   About       1  
3   Contact     1  
4   Legal       2  
5   Privacy     4  
6   Products    1  
7   Support     1  
我要的要求是根据一个分类ID（这个分类ID可能是一个子分类），得到所有的父分类，下面是相应的SQL：
{% highlight sql %}
SELECT T2.id, T2.name 
FROM ( 
    SELECT 
        @r AS _id, 
        (SELECT @r := parent_id FROM table1 WHERE id = _id) AS parent_id, 
        @l := @l + 1 AS lvl 
    FROM 
        (SELECT @r := 5, @l := 0) vars, 
        table1 h 
    WHERE @r <> 0) T1 
JOIN table1 T2 
ON T1._id = T2.id 
ORDER BY T1.lvl DESC 
{% endhighlight %}

代码@r := 5标示查询id为5的所有父类。结果如下  
1, ‘Home’     
2, ‘About’    
4, ‘Legal’    
5, ‘Privacy’  

ps:本文转自[http://blog.sina.com.cn/s/blog_8edc37a80101ij6c.html](http://blog.sina.com.cn/s/blog_8edc37a80101ij6c.html)
