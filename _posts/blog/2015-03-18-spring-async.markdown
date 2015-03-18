---
layout: post
title:  使用spring的@Async异步执行方法
description: 使用spring的@Async异步执行方法
date:   2015-03-18 14:21:39
categories: blog
tags: spring async 异步调用
---
Spring 3.X新增注解@async ，可以标记方法异步运行。方法可为void也可以用Future作为返回值.

如果有返回值，可以使用future.get()得到需要返回的对象.

可以使用future.get(time,unit)，在指定的时间内获取返回值，如果超过设置的时间则抛出异常，异步运行.

[Task Execution and Scheduling](http://docs.spring.io/spring/docs/3.1.x/spring-framework-reference/html/scheduling.html)

applicationContext.xml配置：
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd
    http://www.springframework.org/schema/task
    http://www.springframework.org/schema/task/spring-task-3.0.xsd" default-autowire="byName" default-lazy-init="false">

    <!-- component-scan自动搜索@Component , @Controller , @Service , @Repository等标注的类 -->
    <!-- 默认值如果适合，该项可以不用修改 -->
    <context:component-scan base-package="com.**.service"/>
    <!-- 支持异步方法执行 -->
    <task:annotation-driven/>
</beans>
{% endhighlight %}  

Service类AsyncService：
{% highlight java %}
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Service;

import java.util.concurrent.Future;

/**
 * Created by qince on 2015/3/18.
 */
@Service
public class AsyncService {
    @Async
    public void business() throws InterruptedException {
        System.out.println("业务方法开始执行");
        // 模拟业务操作耗时较长的过程，线程休眠3秒
        Thread.sleep(3000);
        System.out.println("业务方法执行结束");
    }

    /**
     * 得到异步执行结果
     * @return
     * @throws InterruptedException
     */
    @Async
    public Future<Double> business2() throws InterruptedException {
        System.out.println("业务方法开始执行");
        // 模拟业务操作耗时较长的过程，线程休眠3秒
        Thread.sleep(3000);
        System.out.println("业务方法执行结束");
        return new AsyncResult<Double>(100d);
    }
}

{% endhighlight %}

测试类SpringAsyncTest:
{% highlight java %}
import com.company.project.service.AsyncService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.scheduling.annotation.Async;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

/**
 * 使用Spring的@Async注解实现方法异步执行
 * Created by qince on 2015/3/18.
 */

public class SpringAsyncTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:/spring/applicationContext-service.xml");
        AsyncService asyncService = applicationContext.getBean(AsyncService.class);
        SpringAsyncTest springAsyncTest = new SpringAsyncTest();
        springAsyncTest.testAsync(asyncService);
        System.out.println("=========================================");
        springAsyncTest.testAsync2(asyncService);
        System.out.println("=========================================");

        // 注意如果想要异步功能的化，要单独新建一个spring管理的类，不要与其他的Service层的类混用
        try {
            System.out.println("testAsync3 start.");
            springAsyncTest.testAsync3();
            System.out.println("testAsync3 end.");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void testAsync(AsyncService asyncService) {
        System.out.println("使用Spring的@Async注解实现方法异步执行");

        try {
            asyncService.business();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("操作已完成！");
    }

    public void testAsync2(AsyncService asyncService) {

        System.out.println("使用Spring的@Async注解实现方法异步执行2");

        try {
           Future<Double> future = asyncService.business2();
           Double result = future.get();
            System.out.println("result:" + result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        System.out.println("操作已完成！2");
    }

    @Async
    public void testAsync3() throws InterruptedException {
        System.out.println("begin..........");
        Thread.sleep(2000);
        System.out.println("end............");
    }
}

{% endhighlight %}

注意：如果想要异步功能的化，要单独新建一个spring管理的类，不要与其他的Service层的类混用。