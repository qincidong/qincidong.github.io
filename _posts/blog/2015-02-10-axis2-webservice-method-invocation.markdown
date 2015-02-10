---
layout: post
title:  axis2 webservice之方法调用
description: axis2 webservice之方法调用
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---
对于有返回值的方法，使用RPCServiceClient类的invokeBlocking方法调用了WebService中的方法。    
invokeBlocking方法有三个参数，其中第一个参数的类型是QName对象，表示要调用的方法名；  
第二个参数表示要调用的WebService方法的参数值，参数类型为Object[]；  
第三个参数表示WebService方法的返回值类型的Class对象，参数类型为Class[]。  
当方法没有参数时，invokeBlocking方法的第二个参数值不能是null，而要使用new Object[]{}。  

如果被调用的WebService方法没有返回值，应使用RPCServiceClient类的invokeRobust方法，该方法只有两个参数，它们的含义与invokeBlocking方法的前两个参数的含义相同。  
在创建QName对象时，QName类的构造方法的第一个参数表示WSDL文件的命名空间名，也就是<wsdl:definitions>元素的targetNamespace属性值。  
以上内容来自《Axis(Webservice)经典教程》。  

举例：  
调用有返回值的方法：  
Service类；
{% highlight java %}
public class MyService {
    public final Logger logger = Logger.getLogger(MyService.class);
    public String getGreeting(String name) {
        logger.debug("call getGreeting()...");
        return "您好 " + name;
    }
}
{% endhighlight %}

客户端调用：
{% highlight java %}
try {
            RPCServiceClient serviceClient = new RPCServiceClient();
            Options options = serviceClient.getOptions();
            EndpointReference to = new EndpointReference("http://localhost:8080/axis2-ws/services/myService");
            options.setTo(to);
             
            // 调用getGreeting方法，返回值String
            QName qName = new QName("http://webservice.axis2.com","getGreeting");
            String value = (String) serviceClient.invokeBlocking(qName, new Object[]{"bill"}, new Class[]{String.class})[0];
            System.out.println("MyService.getGreeting-->" + value);
             
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
{% endhighlight %}

没有返回值的方法调用：
Service类：
{% highlight java %}
public class MyService {
    public final Logger logger = Logger.getLogger(MyService.class);
    public void update(String data) {
        logger.debug("call update()...");
        System.out.println("<" + data + ">已经更新");
    }
}    
{% endhighlight %}

客户端调用：
{% highlight java %}
package com.axis2.webservice;
 
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;
 
import javax.activation.DataHandler;
import javax.xml.namespace.QName;
 
import org.apache.axis2.AxisFault;
import org.apache.axis2.addressing.EndpointReference;
import org.apache.axis2.client.Options;
import org.apache.axis2.rpc.client.RPCServiceClient;
 
public class MyServiceTest {
 
    /**
     * @param args
     */
    public static void main(String[] args) {
         try {
            RPCServiceClient serviceClient = new RPCServiceClient();
            Options options = serviceClient.getOptions();
            EndpointReference to = new EndpointReference("http://localhost:8080/axis2-ws/services/myService");
            options.setTo(to);
             
            // 调用update方法，无返回值
            // 无返回值的调用应该使用invokeRobust
            qName = new QName("http://webservice.axis2.com", "update");
            serviceClient.invokeRobust(qName, new Object[]{"bill"});
            System.out.println("==========================================>");
             
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
 
}
{% endhighlight %}