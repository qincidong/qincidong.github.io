---
layout: post
title:  Spring3中@Value为Bean的属性注入配置文件配置
description: Spring3中@Value为Bean的属性注入配置文件配置
date:   2015-03-24 16:21:39
categories: blog
tags: spring 属性注入
---
在Spring3中，可以通过@Value注解为Bean的属性注入配置文件中的配置。   
配置文件applicationContext.xml：

{% highlight xml %}
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="com.hyxt"></context:component-scan>

    <bean id="propertyConfiguer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath:config.properties</value>
            </list>
        </property>
    </bean>
</beans>
{% endhighlight %}


一个测试的Service：
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import sun.rmi.transport.ObjectTable;

import java.util.Date;

/**
 * Created by qince on 2015/3/24.
 */
@Service
public class ConfigTestService {

    @Value(value = "${name}")
    private String name;
    @Value(value = "${age}")
    private int age;
    @Value(value = "${birthday}")
    private String birthday;

    public Object[] getConfigs() {
        Object[] configArray = new Object[3];
        configArray[0] = name;
        configArray[1] = age;
        configArray[2] = birthday;

        return configArray;
    }
}

{% endhighlight %}

测试类：
{% highlight java %}
package com.hyxt.cache.ehcache.demo;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import static org.junit.Assert.*;

public class ConfigTestServiceTest {
    private ConfigTestService configTestService;
    @Before
    public void setUp() throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
        configTestService = applicationContext.getBean(ConfigTestService.class);
    }

    @After
    public void tearDown() throws Exception {
        configTestService = null;
    }

    @Test
    public void testGetConfigs() throws Exception {
        System.out.println("测试通过注解的方式注入Bean的属性");
        Object[] result =configTestService.getConfigs();
        if (null != result) {
            for (Object item : result) {
                System.out.println(item);
            }
        }
    }
}
{% endhighlight %}

{% highlight xml %}
<bean id="propertyConfiguer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="locations">
        <list>
            <value>classpath:config.properties</value>
        </list>
    </property>
</bean>
{% endhighlight %}

也可以换成：
{% highlight xml %}
<context:property-placeholder file-encoding="UTF-8" location="config.properties"/>
{% endhighlight %}