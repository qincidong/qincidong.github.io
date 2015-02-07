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

hibernate下获取mysql表中的rownum所遇bug  
在项目中，想要获取mysql的行号，好不容易进行查找进行转换可以得到行号，语句类似于
<pre>
SELECT
	(@rownum := @rownum + 1) AS rownum,
	t.*
FROM
	t_gls_familypic_record t,
	(SELECT(@rownum := 0)) AS s;
</pre>
    
在mysql(5.X版本)中的sql编辑器中可以运行通过，但是在java程序中却抛出异常:org.hibernate.QueryException: Space is not allowed after parameter prefix ':' ....

在网上 查找了资料，却发现这是hibernate3.X包之下的一个bug,(参照 id=41741)在hibernate4.X中已经修复。但是项目中不可能使用hibernate4.0，最后不能不使用原生jdbc进行解决...  
转载：[http://www.educity.cn/wenda/404196.html](http://www.educity.cn/wenda/404196.html)

##解决方法
在[http://stackoverflow.com/questions/9460018/how-can-i-use-mysql-assign-operator-in-hibernate-native-query/9461939](http://stackoverflow.com/questions/9460018/how-can-i-use-mysql-assign-operator-in-hibernate-native-query/9461939)上看到的方法：  
1)在sql中将:=的:改成其他符号，比如|。然后在hibernate拦截器中将|替换成:。原文如下：  
>you can implement this is a slightly different way.. you need to replace the : operator with something else (say '|' char ) and in your interceptor replace the '|' with the : .

>this way hibernate will not try to think the : is a param but will ignore it

>For the interceptor logic you can refer to the hibernate manual

>This has worked for me using MySQL 5.

>remember, this replacing of : must be only done to ':=' and other MySQL specific requirments.. don't try to replace the : for the param-placeholders. (hibernate will not be able to identify the params then)  

2)在:=前后加上/*'*/，因为加上后，hibernate会认为它是一个字符串，就不会解析了。解释的不好，看原文：  
>Another solution for those of us who can't make the jump to Hibernate 4.1.3.
>Simply use /*'*/:=/*'*/ inside the query. Hibernate code treats everything between ' as a string (ignores it). MySQL on the other hand will ignore everything inside a blockquote and will evaluate the whole expression to an assignement operator.
>I know it's quick and dirty, but it get's the job done without stored procedures, interceptors etc.

我目前使用的是第2种方式。
我的需求是计算排名，所以需要使用到行号，但是MySQL又没有提供rownum这样的东西。所以通过下面的方式实现：  
<pre>
SELECT
	(@rownum := @rownum + 1) AS rownum,
	t.*
FROM
	t_gls_familypic_record t,
	(SELECT(@rownum := 0)) AS s;
</pre>

所以我最终的sql是这样的：  
<pre>
String sql = "select * from ("
    +"select (@rowNum/*'*/:=/*'*/@rowNum+1) as rowNo,t.* from ("
    +"select a.NICKNAME,a.PHOTOURL,a.MEMBERID,b.voteCount from t_member a,("
    +"select memberid, count(*) voteCount from t_gls_familypic_record where glshbactivityid =:activityid group by memberid) b "
    +"where a.MEMBERID = b.memberid order by voteCount desc) t,(Select (@rowNum/*'*/:=/*'*/0) ) f) s where s.memberid!=:memberid";
SQLQuery query = sessionFactory.getCurrentSession().createSQLQuery(sql);
query.setParameter("activityid", activityid);
query.setParameter("memberid", memberid);

return query.setResultTransformer(Criteria.ALIAS_TO_ENTITY_MAP).setFirstResult(currentIndex).setMaxResults(maxResult).list();
</pre>