---
layout: post
title:  Axis Webservice之跨服务会话管理
description: Axis Webservice之跨服务会话管理
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---                                                                                                         
实现跨服务会话管理的步骤如下：  
1）使用MessageContext和ServiceGroupContext获得与设置key-value对。  
2）为要进行Session管理的WebService类所对应的<service>元素添加一个scope属性，并将该属性值设为application。  
3） 在客户端使用setManageSession(true)打开Session管理功能。  
下面是一个实现跨服务会话管理的例子：  
第一个Service：  
{% highlight java %}
package com.axis2.webservice;
 
import org.apache.axis2.context.MessageContext;
import org.apache.axis2.context.ServiceGroupContext;
 
// Axis2会话管理
public class SessionManagementService {
    public boolean login(String username,String pwd) {
        if ("admin".equals(username) && "123456".equals(pwd)) {
            MessageContext mc = MessageContext.getCurrentMessageContext();
            ServiceGroupContext  sc = mc.getServiceGroupContext();
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
        ServiceGroupContext sc = mc.getServiceGroupContext();
        String username = (String) sc.getProperty("username");
        String pwd = (String) sc.getProperty("password");
        String status = (String) sc.getProperty("status");
        return "username:" + username + ",password:" + pwd + ",status:" + status;
    }
}
{% endhighlight %}

第二个Service：
{% highlight java %}
package com.axis2.webservice;
 
import org.apache.axis2.context.MessageContext;
import org.apache.axis2.context.ServiceGroupContext;
 
// 跨服务会话管理
public class SearchService {
    public String getUserByName(String name) {
        MessageContext mc = MessageContext.getCurrentMessageContext();
        ServiceGroupContext sc = mc.getServiceGroupContext();
        String user = (String) sc.getProperty("username");
        if (user != null && user.equals(name)) {
            return "找到用户<" + name + ">";
        }
        return "用户未登陆！";
    }
}
{% endhighlight %}

{% highlight xml %}
<serviceGroup>
    <!-- transportsession:单个Service的各个操作之间会话共享 -->
    <service name="sessionManagementService" scope="application">
        <parameter name="ServiceClass">
            com.axis2.webservice.SessionManagementService
        </parameter>
        <messageReceivers>
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-only" class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-out"  class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </messageReceivers>
    </service>
     
    <!-- 跨服务会话共享需要使用application -->
    <service name="userSearchService" scope="application">
        <parameter name="ServiceClass">
            com.axis2.webservice.SearchService
        </parameter>
        <messageReceivers>
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-only" class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-out"  class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </messageReceivers>
    </service>
</serviceGroup>
{% endhighlight %}

客户端调用：
{% highlight java %}
package com.axis2.webservice;
 
import javax.xml.namespace.QName;
 
import org.apache.axis2.AxisFault;
import org.apache.axis2.addressing.EndpointReference;
import org.apache.axis2.client.Options;
import org.apache.axis2.rpc.client.RPCServiceClient;
 
public class TestSearchService {
 
    /**
     * @param args
     */
    public static void main(String[] args) {
        try {
            RPCServiceClient client = new RPCServiceClient();
            Options options = client.getOptions();
            options.setManageSession(true);
            options.setTo(new EndpointReference("http://127.0.0.1:8080/axis2-ws/services/sessionManagementService"));
            QName qName = new QName("http://webservice.axis2.com","login");
            boolean loginFlag = (Boolean) client.invokeBlocking(qName, new Object[]{"admin","123456"}, new Class[]{Boolean.class})[0];
            System.out.println(loginFlag);
             
            System.out.println(">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>");
             
            qName = new QName("http://webservice.axis2.com","getLoginStatus");
            String msg = (String) client.invokeBlocking(qName, new Object[]{}, new Class[]{String.class})[0];
            System.out.println(msg);
 
            System.out.println(">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>");
            options.setTo(new EndpointReference("http://127.0.0.1:8080/axis2-ws/services/userSearchService"));
            qName = new QName("http://webservice.axis2.com",
                    "getUserByName");
            msg = (String) client.invokeBlocking(qName,
                    new Object[] { "admin" }, new Class[] { String.class })[0];
            System.out.println(msg); // 用户未登陆
             
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
 
}
{% endhighlight %}

注：这并不是同一个工程哦，只是包名一样而已。

输出：  
true  
username:admin,password:123456,status:登陆成功！  
找到用户<admin>  