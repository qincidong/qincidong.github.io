---
layout: post
title:  Hibernate异常汇总
description: Hibernate异常汇总
date:   2015-01-13 16:49:39
categories: blog
tags: hibernate exception
---
##1.unexpected token: (
异常信息如下：  
Caused by: org.hibernate.hql.ast.QuerySyntaxException: unexpected token: ( near line 1, column 117 [SELECT t.goodsid,t.shopid,t.goodsname,t.contents,t.remark,p.picurl,m.price,m.gtype,m.total FROM t_goods t left join (select goodsid,picurl from t_picture group by GOODSID) p on p.goodsid = t.goodsid left join t_model m on m.goodsid = t.GOODSID where t.shopid = :shopid and t.status = 1 ORDER BY t.createtime DESC]

sql如下：
<pre>
SELECT
	t.goodsid,
	t.shopid,
	t.goodsname,
	t.contents,
	t.remark,
	p.picurl,
	m.price,
	m.gtype,
	m.total
FROM
	t_goods t
left join (select goodsid,picurl from t_picture group by GOODSID) p on p.goodsid = t.goodsid
left join t_model m on m.goodsid = t.GOODSID
where t.shopid = :shopid
  and t.status = 1
ORDER BY
	t.createtime DESC;
</pre>	
报这个错，主要是因为我用的Query，非SQLQuery。
<pre>Query query = sessionFactory.getCurrentSession().createQuery(sql);</pre>


##2.No Dialect mapping for JDBC type: -1
出现这个问题的原因是 通过 Hibernate  createSQLQuery() 方法进行查询，对应表中的列有 text类型的，方言导致的。   
解决方法：自已建一个方言，继承于MySQLDialect ，引入 <pre>registerHibernateType(Types.LONGVARCHAR, Hibernate.TEXT.getName());</pre>   
然后将hibernate注册的方言改成自己的。如： 
<pre>
<prop key="hibernate.dialect"> 
com.unix360.util.BlobMySQLDialect 
</prop> 
</pre>
代码如下： 
<pre>
package com.unix360.util;
import java.sql.Types;

import org.hibernate.Hibernate;
import org.hibernate.dialect.MySQLDialect;

public class BlobMySQLDialect extends MySQLDialect {
      public BlobMySQLDialect(){
            super();
            registerHibernateType(Types.LONGVARCHAR, Hibernate.TEXT.getName());
          }

}
</pre>
