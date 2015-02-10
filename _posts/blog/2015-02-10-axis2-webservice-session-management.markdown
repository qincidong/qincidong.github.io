---
layout: post
title:  Axis2 Webservice之会话管理
description: Axis2 Webservice之会话管理
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---                                                                                                         
摘自：《Axis2(WebService)经典教程》                                                                                                                               
WebService给人最直观的感觉就是由一个个方法组成，并在客户端通过SOAP协议调用这些方法。                                                                              
这些方法可能有返回值，也可能没有返回值。                                                                                                                          
虽然这样可以完成一些工具，但这些被调用的方法是孤立的，当一个方法被调用后，在其他的方法中无法获得这个方法调用后的状态，也就是说无法保留状态。                      
读者可以想象，这对于一个完整的应用程序，无法保留状态，就意味着只依靠WebService很难完成全部的工作。                                                                
例如，一个完整的应用系统都需要进行登录，这在Web应用中使用Session来保存用户登录状态，而如果用WebService的方法来进行登录处理，无法保存登录状态是非常令人尴尬的。    
当然，这也可以通过其他的方法来解决，如在服务端使用static变量来保存用户状态，并发送一个id到客户端，通过在服务端和客户端传递这个id来取得相应的用户状态。            
这非常类似于Web应用中通过Session和Cookie来管理用户状态。但这就需要由开发人员做很多工作，不过幸好Axis2为我们提供了WebService状态管理的功能。                       
使用Axis2来管理WebService的状态基本上对于开发人员是透明的。                                                                                                       
在WebService类需要使用org.apache.axis2.context.MessageContext和org.apache.axis2.context.ServiceContext类来保存与获得保存在服务端的状态信息，                      
这有些象使用HttpSession接口的getAttribute和setAttribute方法获得与设置Session域属性。                                                                              
除此之外，还需要修改services.xml文件的内容，为<service>元素加一个scope属性，该属性有四个可取的值：Application, SOAPSession, TransportSession, Request，           
不过要注意一下，虽然Axis2的官方文档将这四个值的单词首字母和缩写字母都写成了大写，但经笔者测试，必须全部小写才有效，                                               
也就是这四个值应为：application、soapsession、transportsession、request，其中request为scope属性的默认值。                                                         
读者可以选择使用transportsession和application分别实现同一个WebService类和跨WebService类的会话管理。                                                               
在客户端需要使用setManageSession(true)打开Session管理功能。                                                                                                       
综上所述，实现同一个WebService的Session管理需要如下三步：                                                                                                         
1. 使用MessageContext和ServiceContext获得与设置key-value对。                                                                                                      
2. 为要进行Session管理的WebService类所对应的<service>元素添加一个scope属性，并将该属性值设为transportsession。                                                    
3. 在客户端使用setManageSession(true)打开Session管理功能。                                                                                                        
下面是一个在同一个WebService类中管理Session的例子。                                                                                                               

{% highlight java %}
package com.axis2.webservice;
 
import org.apache.axis2.context.MessageContext;
import org.apache.axis2.context.ServiceContext;
 
// Axis2会话管理
public class SessionManagementService {
    public boolean login(String username,String pwd) {
        if ("admin".equals(username) && "123456".equals(pwd)) {
            MessageContext mc = MessageContext.getCurrentMessageContext();
            ServiceContext sc = mc.getServiceContext();
            sc.setProperty("username", username);
            sc.setProperty("password", pwd);
            sc.setProperty("status", "登陆成功！");
            return true;
        }
        else {
            return false;
        }
    }
     
    public String getLoginStatus() {
        MessageContext mc = MessageContext.getCurrentMessageContext();
        ServiceContext sc = mc.getServiceContext();
        String username = (String) sc.getProperty("username");
        String pwd = (String) sc.getProperty("password");
        String status = (String) sc.getProperty("status");
        return "username:" + username + ",password:" + pwd + ",status:" + status;
    }
}
{% endhighlight %}

services.xml:
{% highlight xml %}
<serviceGroup>
    <!-- transportsession:单个Service的各个操作之间会话共享 -->
    <service name="sessionManagementService" scope="transportsession">
        <parameter name="ServiceClass">
            com.axis2.webservice.SessionManagementService
        </parameter>
        <messageReceivers>
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-only" class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-out"  class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </messageReceivers>
    </service>
</serviceGroup>
{% endhighlight %}

客户端：
{% highlight java %}
package com.axis2.webservice;
 
import javax.xml.namespace.QName;
 
import org.apache.axis2.AxisFault;
import org.apache.axis2.addressing.EndpointReference;
import org.apache.axis2.client.Options;
import org.apache.axis2.rpc.client.RPCServiceClient;
 
public class SessionManagementServiceTest {
 
    /**
     * @param args
     */
    public static void main(String[] args) {
        RPCServiceClient serviceClient;
        try {
            serviceClient = new RPCServiceClient();
            Options options = serviceClient.getOptions();
            options.setManageSession(true);
            options.setTo(new EndpointReference("http://127.0.0.1:8080/axis2-ws/services/sessionManagementService"));
             
            QName qName = new QName("http://webservice.axis2.com", "login");
            Boolean loginFlag = (Boolean) serviceClient.invokeBlocking(qName, new Object[]{"admin","123456"}, new Class[]{Boolean.class})[0];
            System.out.println(loginFlag);
             
            System.out.println(">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>");
             
            qName = new QName("http://webservice.axis2.com", "getLoginStatus");
            String str = (String) serviceClient.invokeBlocking(qName, new Object[]{}, new Class[]{String.class})[0];
            System.out.println(str);
        } catch (AxisFault e) {
            e.printStackTrace();
        }
         
    }
 
}
{% endhighlight %}

输出：  
true   
username:admin,password:123456,status:登陆成功！  