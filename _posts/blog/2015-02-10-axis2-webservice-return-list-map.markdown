---
layout: post
title:  Axis2 Webservice之返回List、Map
description: Axis2 Webservice之返回List、Map
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---
List和Map都没有实现序列化接口，不能在网络上传输数据。
因此Axis2的service方法中也不能返回List和Map，否则会报类型转换异常。
其实有一种变通的方式可以实现这个目的，那就是使用Json。
在service方法中返回List或Map对应的JSON字符串，客户端进行解析得到List或Map。
如下：
service类：  
{% highlight java %}
package com.axis2.webservice;
 
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
 
import net.sf.json.JSONArray;
import net.sf.json.JSONObject;
 
public class TestService {
     
    // 返回集合或Map等可以通过返回Json字符串
    // 在客户端解析得到List或Map
    public String getListString() {
        List<String> strList = new ArrayList<String>();
        strList.add("hello");
        strList.add("world");
        strList.add("!!!");
        JSONArray jsonArray = JSONArray.fromObject(strList);
        System.out.println(jsonArray.toString());
        return jsonArray.toString();
    }
     
    public String getMapString() {
        Person p1 = new Person();
        p1.setAge(10);
        p1.setName("儿童");
         
        Person p2 = new Person();
        p2.setAge(20);
        p2.setName("成年");
         
        Map<String,Person> map = new HashMap<String,Person>();
        map.put("p1", p1);
        map.put("p2", p2);
         
        JSONObject jsonObj = JSONObject.fromObject(map);
        return jsonObj.toString();
    }
}
{% endhighlight %}

客户端调用：
{% highlight java %}
package com.axis2.webservice;
 
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
 
import javax.xml.namespace.QName;
 
import net.sf.json.JSONArray;
import net.sf.json.JSONObject;
 
import org.apache.axis2.AxisFault;
import org.apache.axis2.addressing.EndpointReference;
import org.apache.axis2.client.Options;
import org.apache.axis2.rpc.client.RPCServiceClient;
 
public class TestServiceTest {
 
    /**
     * @param args
     */
    public static void main(String[] args) {
        RPCServiceClient serviceClient;
        try {
            serviceClient = new RPCServiceClient();
            Options options = serviceClient.getOptions();
            options.setTo(new EndpointReference("http://127.0.0.1:8080/axis2-ws/services/testService"));
             
            // Service返回集合字符串，客户端通过Json转换得到List
            qName = new QName("http://webservice.axis2.com","getListString");
            String jsonStr = (String) serviceClient.invokeBlocking(qName, new Object[]{}, new Class[]{String.class})[0];
            JSONArray jsonArray = JSONArray.fromObject(jsonStr);
            for (int i=0;i<jsonArray.size();i++) {
                Object value = jsonArray.get(i);
                System.out.println(value);
            }
            System.out.println(">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>");
             
            // Service段返回Map字符串，客户端通过JSON解析得到Map
            qName = new QName("http://webservice.axis2.com","getMapString");
            jsonStr = (String) serviceClient.invokeBlocking(qName, new Object[]{}, new Class[]{String.class})[0];
            System.out.println(jsonStr);
            JSONObject jsonObj = JSONObject.fromObject(jsonStr);
            Map<String,Object> map = jsonObj;
            Iterator<String> its = map.keySet().iterator();
            while (its.hasNext()) {
                String key = its.next();
                Object value = map.get(key);
//              System.out.println("key:" + key + ",value:" + value);
                Person p = (Person) JSONObject.toBean((JSONObject)value,Person.class);
                System.out.println(p);
            }
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
 
}
{% endhighlight %}