---
layout: post
title:  基于Spring AOP结合EHCache实现缓存
description: 基于Spring AOP结合EHCache实现查找时放入缓存，更新删除时清除缓存。
date:   2015-03-24 14:21:39
categories: blog
tags: spring ehcache cache
---
结合Spring AOP和EHCache实现查找时将结果放入缓存，在更新或删除操作时清除缓存的功能。  
项目结构如下：
![img](http://uploadingit.com/file/nsott7k9hmj5x5if/spring_ehcache_project_structure.png)
依赖的Jars如下：
![img](http://uploadingit.com/file/d9k9o2pumxxo2gad/spring_ehcache_jars.png)

ehcache.xml:
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="ehcache.xsd" updateCheck="true"
         monitoring="autodetect">
    <!--
    <diskStore path="java.io.tmpdir" /> -->
    <diskStore path="d:/cachetmpdir"/>
    <defaultCache maxElementsInMemory="10000" eternal="false"
                  timeToIdleSeconds="120" timeToLiveSeconds="120" overflowToDisk="true"
                  maxElementsOnDisk="10000000" diskPersistent="false"
                  diskExpiryThreadIntervalSeconds="120" memoryStoreEvictionPolicy="LRU" />

    <cache name="dbCache" maxElementsInMemory="10000"
           maxElementsOnDisk="1000" eternal="false" overflowToDisk="true"
           diskSpoolBufferSizeMB="20" timeToIdleSeconds="300" timeToLiveSeconds="600"
           memoryStoreEvictionPolicy="LFU" />
</ehcache>
{% endhighlight %}

applicationContext-cache.xml:
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ehcache="http://www.springframework.org/schema/cache"
       xmlns:cache="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-3.1.xsd http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd">
    <bean id="defaultCacheManager" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
        <property name="configLocation" value="ehcache.xml"></property>
    </bean>

    <bean id="ehCache" class="org.springframework.cache.ehcache.EhCacheFactoryBean">
        <property name="cacheManager" ref="defaultCacheManager"></property>
        <property name="cacheName" value="dbCache"></property>
    </bean>

    <!--find/create Cache拦截器-->
    <bean id="methodCacheInterceptor" class="com.hyxt.cache.ehcache.demo.MethodCacheInterceptor">
        <property name="cache" ref="ehCache"></property>
    </bean>

    <!--update/delete Cache拦截器-->
    <bean id="methodCacheAfterAdvice" class="com.hyxt.cache.ehcache.demo.MethodCacheAfterReturningAdvice">
        <property name="cache" ref="ehCache"></property>
    </bean>

    <bean id="methodCacheAfterReturningAdvice" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
        <property name="advice" ref="methodCacheInterceptor"></property>
        <property name="patterns">
            <list>
                <value>.*find.*</value>
                <value>.*get.*</value>
            </list>
        </property>
    </bean>

    <bean id="methodCachePointcutAfterReturning" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
        <property name="advice" ref="methodCacheAfterAdvice"></property>
        <property name="patterns">
            <list>
                <value>.*create.*</value>
                <value>.*update.*</value>
                <value>.*delete.*</value>
            </list>
        </property>

    </bean>
</beans>
{% endhighlight %}

applicationContext.xml:
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    <import resource="applicationContext-cache.xml"></import>
    <context:component-scan base-package="com.hyxt"></context:component-scan>

    <bean id="testServiceTarget" class="com.hyxt.cache.ehcache.demo.TestService"></bean>
    <bean id="testService" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="testServiceTarget"></property>
        <property name="interceptorNames">
            <list>
                <value>methodCacheAfterReturningAdvice</value>
                <value>methodCachePointcutAfterReturning</value>
            </list>
        </property>
    </bean>
</beans>
{% endhighlight %}

MethodCacheInterceptor.java:
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import net.sf.ehcache.Cache;
import net.sf.ehcache.Element;
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.util.Assert;

/**
 * 该拦截器的作用是在执行方法时查询/新建Cache。
 * Created by qince on 2015/3/23.
 */
public class MethodCacheInterceptor implements MethodInterceptor,InitializingBean {
    private final static Log log = LogFactory.getLog(MethodCacheInterceptor.class);
    private Cache cache;

    public Cache getCache() {
        return cache;
    }

    public void setCache(Cache cache) {
        this.cache = cache;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        Assert.notNull(cache,"Need a cache,please use setCache(Cache) to create it!");
    }

    @Override
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        // 类名+方法名+方法参数作为Cache Key
        String className = methodInvocation.getThis().getClass().getName();
        String methodName = methodInvocation.getMethod().getName();
        Object[] arguments = methodInvocation.getArguments();

        String cacheKey = getCacheKey(className, methodName, arguments);
        log.debug("cacheKey:" + cacheKey);

        Element element = null;
        synchronized (this) {
            element = cache.get(cacheKey);
            if (null == element) {
                log.info("未从缓存查找到数据，从数据库查询。。");

                Object result = methodInvocation.proceed();
                element = new Element(cacheKey,result);
                cache.put(element);
            }
            else {
                log.info("从缓存中查找到数据 :" + cacheKey);
            }
        }

        return element.getValue();
    }

    public String getCacheKey(String className,String methodName,Object[] arguments) {
        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append(className).append(".").append(methodName);
        if (null != arguments) {
            for (Object argument : arguments) {
                stringBuffer.append(".").append(argument);
            }
        }
        return stringBuffer.toString();
    }
}
{% endhighlight %}

MethodCacheAfterReturningAdvice.java:
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import net.sf.ehcache.Cache;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.aop.AfterReturningAdvice;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.util.Assert;

import java.lang.reflect.Method;
import java.util.List;

/**
 * 该类的作用是执行update/delete操作时，清除缓存
 * Created by qince on 2015/3/23.
 */
public class MethodCacheAfterReturningAdvice implements AfterReturningAdvice,InitializingBean {
    private final static Log log = LogFactory.getLog(MethodCacheInterceptor.class);
    private Cache cache;

    public Cache getCache() {
        return cache;
    }

    public void setCache(Cache cache) {
        this.cache = cache;
    }
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        String className =o1.getClass().getName();
        System.out.println("className;" + className);
//        System.out.println("o:" + o.getClass().getName());
        System.out.println("method:" + method.getClass().getName());
        List cacheKeys =cache.getKeys();

        if (null != cacheKeys) {
            for (int i=0;i<cacheKeys.size();i++) {
                String cacheKey = cacheKeys.get(i).toString();
                if (cacheKey.startsWith(className)) {
                    cache.remove(cacheKey);
                    log.info("清除缓存 : " + cacheKey);
                }
            }
        }
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        Assert.notNull(cache, "Need a cache,please use setCache(Cache) to create it!");
    }
}
{% endhighlight %}

TestDao.java:
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.stereotype.Repository;

/**
 * Created by qince on 2015/3/23.
 */
@Repository
public class TestDao {
    Log log = LogFactory.getLog(TestDao.class);
    public Object getObject(String name) {
        log.info("从数据库查找，name=" + name);
        return "hello " + name;
    }

    public void updateObject(String name) {
        log.info("更新数据库");
    }
}

{% endhighlight %}

TestService.java:
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

/**
 * Created by qince on 2015/3/23.
 */
public class TestService {
    private final static Log LOG = LogFactory.getLog(TestService.class);
    @Autowired
    private TestDao testDao;

    public Object getObject(String name) {
        return testDao.getObject(name);
    }

    public void updateObject(String name) {
        testDao.updateObject(name);
    }
}
{% endhighlight %}

EHCacheTest.java:
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.omg.CORBA.portable.ApplicationException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Created by qince on 2015/3/23.
 */
public class EHCacheTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestService testService = (TestService) applicationContext.getBean("testService");
        System.out.println("第一次查找并创建Cache。");
        testService.getObject("test");
        System.out.println("=============================");
        System.out.println("第二次从Cache中查找。");
        testService.getObject("test");
        System.out.println("=============================");
        System.out.println("第三次清除Cache。");
        testService.updateObject("test");
        System.out.println("=============================");
        System.out.println("第四次从数据库查找并放入Cache。");
        testService.getObject("test");
    }
}
{% endhighlight %}

执行结果：
![img](http://uploadingit.com/file/nc8mtxibkbozhibh/spring_ehcache_console.png)

