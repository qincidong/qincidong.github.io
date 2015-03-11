---
layout: post
title:  hibernate集群环境表主键的问题
description: hibernate集群环境表主键的问题
date:   2015-03-11 20:11:39
categories: blog
tags: tomcat hibernate 集群 主键 
---
在集群环境不要使用hibernate管理主键，如：
{% highlight java %}
@Id @GeneratedValue(generator="custom-id")
	@GenericGenerator(name="custom-id", strategy = "increment")
	@Column(name = "ID", unique = true, nullable = false, insertable = true, updatable = true, length = 19)
	public java.lang.Long getId() {
		return this.id;
	}
{% endhighlight %}  

因为每个Hibernate都有一个实例维护主键的自增.所以多个tomcat运行时会出现id重复的问题!  
所以最好使用数据库自动生成主键，如：
{% highlight java %}
@Id @GeneratedValue(generator="custom-id")
	@GenericGenerator(name="custom-id", strategy = "identity")
	@Column(name = "ID", unique = true, nullable = false, insertable = true, updatable = true, length = 19)
	public java.lang.Long getId() {
		return this.id;
	}
{% endhighlight %}  