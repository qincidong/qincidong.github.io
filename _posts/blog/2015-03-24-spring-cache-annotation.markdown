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
{% highlight xml %}
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd  
                http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-3.1.xsd
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/context/spring-context-3.0.xsd">

       <context:component-scan base-package="com.hyxt"></context:component-scan>
       <!-- 启用缓存注解功能，这个是必须的，否则注解不会生效，另外，该注解一定要声明在spring主配置文件中才会生效 -->
       <cache:annotation-driven cache-manager="cacheManager"/>


       <!-- spring自己的换管理器，这里定义了两个缓存位置名称 ，既注解中的value -->
       <bean id="cacheManager" class="org.springframework.cache.support.SimpleCacheManager">
              <property name="caches">
                     <set>
                            <bean
                                    class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
                                    p:name="default" />
                            <bean
                                    class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
                                    p:name="dbCache" />
                     </set>
              </property>
       </bean>
</beans>
{% endhighlight %}
