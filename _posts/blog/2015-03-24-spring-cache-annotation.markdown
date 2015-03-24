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
