---
layout: post
title:  注解方式使用spring自带的Cache
description: 注解方式使用spring自带的Cache
date:   2015-03-24 14:21:39
categories: blog
tags: spring cache
---
Spring3.1提供Cache的是spring-context模块。ConcurrentMapCacheFactoryBean提供获取缓存的功能ConcurrentMapCache。  
跟踪堆栈发现它实际上最终是通过ConcurrentMap来实现的。  
配置文件applicationContext-cache-annotation.xml：
