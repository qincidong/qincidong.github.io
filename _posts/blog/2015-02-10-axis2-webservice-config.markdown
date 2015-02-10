---
layout: post
title:  Axis Webservice配置说明
description: Axis Webservice配置说明
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---                                                                                                         
多个服务配置使用serviceGroup标签。                                                                          
单个服务就不需要<serviceGroup>标签了。                                                                      
<service>标签中的<description>用来描述该服务的作用。                                                        
ServiceClass参数用于指定服务类的位置                                                                        
operation用来指定服务方法。                                                                                 
必须为每个服务配置消息接收器，有2种方式：                                                                   
#1.在operation标签中指定messageReceiver;                                                                    
#2.配置服务级参数messageReceivers。                                                                         
如果一个方法有返回值就使用RPCMessageReceiver，没有返回值使用RPCInOnlyMessageReceiver。                      
<service>标签中scope属性有如下几个值：                                                                      
application、soapsession、transportsession、request，其中request为scope属性的默认值。用于实现会话管理。     
下面是一个例子；                                                                                            
services.xml:
{% highlight xml %}
<serviceGroup>
    <service name="myService">
        <description>
            Web Service例子
        </description>
        <parameter name="ServiceClass">
            com.axis2.webservice.MyService
        </parameter>
        <operation name="getGreeting">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="update">
            <messageReceiver
                class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
        </operation>
        <operation name="getStringArray">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="getPerson">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="getPersonArray">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="getList">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="getListSize">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="uploadImage">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <operation name="uploadFile">
            <messageReceiver class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </operation>
        <!-- <messageReceivers>
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-out" class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-only" class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
            </messageReceivers> -->
    </service>
    <service name="testService">
        <parameter name="ServiceClass">
            com.axis2.webservice.TestService
        </parameter>
        <messageReceivers>
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-only" class="org.apache.axis2.rpc.receivers.RPCInOnlyMessageReceiver" />
            <messageReceiver mep="http://www.w3.org/2004/08/wsdl/in-out"  class="org.apache.axis2.rpc.receivers.RPCMessageReceiver" />
        </messageReceivers>
    </service>
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