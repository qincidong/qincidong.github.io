---
layout: post
title:  Spring3.1结合EHCache实现注解缓存
description: Spring3.1结合EHCache实现注解缓存
date:   2015-03-24 15:21:39
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

    <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager" p:cache-manager-ref="ehcache" />
    <bean id="ehcache" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean" p:config-location="classpath:/ehcache.xml" />
</beans>
{% endhighlight %}

cacheManger中的caches可以配置多个。在使用Cache注解时指定缓存的位置。
一个测试的Service：
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

/**
 * Created by qince on 2015/3/24.
 */
@Service
public class HelloService {
    private final static Log LOG = LogFactory.getLog(HelloService.class);

    @Cacheable(value = "dbCache",key = "'getMsg_' + #id")
    public String getMsg(String id) {
        LOG.info("call getMsg(id) by " + id);
        return "hello " + id;
    }

    @CacheEvict(value = "dbCache",key = "'getMsg_' + #id")
    public void updateMsg(String id) {
        LOG.info("updateMsg by id " + id);
    }
}
{% endhighlight %}

测试类：
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import static org.junit.Assert.*;

public class HelloServiceTest {
    private HelloService helloService;
    @org.junit.Before
    public void setUp() throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:applicationContext-cache-annotation.xml");
        helloService = applicationContext.getBean(HelloService.class);
    }

    @org.junit.After
    public void tearDown() throws Exception {
        helloService = null;
    }

    @org.junit.Test
    public void testGetMsg() throws Exception {
        String msg = helloService.getMsg("10001");
        System.out.println(msg);
        msg = helloService.getMsg("10001");
        System.out.println(msg);
    }

    @org.junit.Test
    public void testUpdateMsg() throws Exception {
        String msg = helloService.getMsg("10001");
        System.out.println(msg);
        helloService.updateMsg("10001");
        msg = helloService.getMsg("10001");
        System.out.println(msg);
    }
}
{% endhighlight %}

3.1同样提供了两个注解：  
@Cacheable：负责将方法的返回值加入到缓存中  
@CacheEvict：负责清除缓存

@Cacheable 支持如下几个参数：  
value：缓存位置名称，不能为空，如果使用EHCache，就是ehcache.xml中声明的cache的name  
key：缓存的key，默认为空，既表示使用方法的参数类型及参数值作为key，支持SpEL  
condition：触发条件，只有满足条件的情况才会加入缓存，默认为空，既表示全部都加入缓存，支持SpEL


@CacheEvict 支持如下几个参数：  
value：缓存位置名称，不能为空，同上  
key：缓存的key，默认为空，同上  
condition：触发条件，只有满足条件的情况才会清除缓存，默认为空，支持SpEL  
allEntries：true表示清除value中的全部缓存，默认为false  

一般来说，我们的更新操作只需要刷新缓存中某一个值，所以定义缓存的key值的方式就很重要，最好是能够唯一，因为这样可以准确的清除掉特定的缓存，而不会影响到其它缓存值

SpEL：Spring Expression Language
关于SpEL的介绍，可以参考如下地址：
[SpEL](http://static.springsource.org/spring/docs/3.1.0.M1/spring-framework-reference/html/expressions.html)

参考：[Spring3.1 Cache注解](http://hanqunfeng.iteye.com/blog/1158824)&nbsp;&nbsp;[Spring3.1缓存Cache配置](http://liuccc1.iteye.com/blog/1345195)