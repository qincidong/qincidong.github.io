---
layout: post
title:  mysql 实现行号的方法——如何获取当前记录所在行号
description: mysql 实现行号的方法——如何获取当前记录所在行号
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
{% highlight sql %}
Select UID,(@rowNum:=@rowNum+1) as rowNo
From a,
(Select (@rowNum :=0) ) b
Order by a.Money Desc
{% endhighlight %}

输入结果如下：  
UID	       rowNo
4	       1
3	       2
2	       3
1	       4

转载：[http://www.cnblogs.com/xinlei/archive/2011/12/16/2290349.html](http://www.cnblogs.com/xinlei/archive/2011/12/16/2290349.html)

hibernate下获取mysql表中的rownum所遇bug  
在项目中，想要获取mysql的行号，好不容易进行查找进行转换可以得到行号，语句类似于
“set @rownum=0, @preval=null; select @rnk:=IF((@rownum := @rownum + 1) and (@preval <=> sal),
 @rnk, @rownum) AS rnk, @preval:=sal sal FROM emp order by sal;”  
 在mysql(5.X版本)中的sql编辑器中可以运行通过，但是在java程序中却抛出异常:org.hibernate.QueryException: Space is not allowed after parameter prefix ':' ....

在网上 查找了资料，却发现这是hibernate3.X包之下的一个bug,(参照 id=41741)在hibernate4.X中已经修复。但是项目中不可能使用hibernate4.0，最后不能不使用原生jdbc进行解决...  
转载：[http://www.educity.cn/wenda/404196.html](http://www.educity.cn/wenda/404196.html)