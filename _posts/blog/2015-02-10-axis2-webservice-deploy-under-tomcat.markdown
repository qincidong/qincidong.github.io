---
layout: post
title:  Axis2 Webservice之部署服务到tomcat
description: Axis2 Webservice之部署服务到tomcat
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---
![title](http://d.hiphotos.bdimg.com/album/pic/item/e1fe9925bc315c607f16101a8fb1cb1348547747.jpg)
如上是我的工程结构图，因为并不是单独的写webservice的类，所以将webservice相关的类和配置文件放在com.axis2.webservice包。
下面要说的就是怎么将webservice类和配置文件进行打包，部署到tomcat。
我使用的Ant打包aar，build.xml如下：
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project name="axis2WebService" default="aar" basedir=".">
    <property name="lib.dir" value="lib">
    </property>
    <property name="src.dir" value="src">
    </property>
    <property name="dest.dir" value="dest">
    </property>
    <property name="classes.dir" value="${dest.dir}/classes">
    </property>
    <property name="jarfile" value="${dest.dir}/helloworldAnt.jar">
    </property>
    <property name="aarfile" value="${dest.dir}/webservice/axis2-ws.aar">
    </property>
    <property name="webserviceClass" value="${classes.dir}/com/axis2/webservice/">
    </property>
 
    <target name="init">
        <mkdir dir="${classes.dir}">
        </mkdir>
        <echo message="Create dir ${dest.dir}.">
        </echo>
    </target>
 
    <path id="master-classpath">
        <fileset dir="${lib.dir}">
            <include name="**/*.jar" />
        </fileset>
    </path>
    <target name="compile" depends="init">
        <javac srcdir="${src.dir}" destdir="${classes.dir}" classpathref="master-classpath">
        </javac>
        <echo>Compile source code.</echo>
    </target>
 
    <target name="jar" depends="compile">
        <jar jarfile="${jarfile}" basedir="${classes.dir}" includes="**/*.class">
        </jar>
        <echo>Create jar file.</echo>
    </target>
     
    <!--
        Create Axis2 WebService Jar
        这个其实是我webservice类中用到一个自定义的类，这里将这个类打包成jar
        供客户端引用
    -->
    <target name="axis2-jar" depends="compile">
        <jar jarfile="${dest.dir}/axis2-server-beans.jar" basedir="${classes.dir}" includes="com/axis2/webservice/Person.class"></jar>
        <echo>Create Axis Webservice Jar.</echo>
    </target>
 
    <!-- 生成aar文件 -->
    <target name="aar" depends="compile">
        <copy file="${src.dir}/com/axis2/webservice/services.xml" todir="${dest.dir}/webservice/tmp/META-INF/" />
        <copy todir="${dest.dir}/webservice/tmp/">
            <fileset dir="${classes.dir}">
                <include name="com/axis2/webservice/**" />
            </fileset>
        </copy>
        <jar jarfile="${aarfile}">
            <fileset dir="${dest.dir}/webservice/tmp/">
                <include name="com/**">
                </include>
                <include name="META-INF/**">
                </include>
                <exclude name="**/http/**">
                </exclude>
            </fileset>
        </jar>
        <echo>Create Axis2 Webservice aar file.</echo>
        <!--生成WEBSERVICE WAR包-->
        <copy file="${src.dir}/com/axis2/webservice/web.xml" todir="${dest.dir}/axis2-ws.war/WEB-INF">
        </copy>
        <copy file="${dest.dir}/webservice/axis2-ws.aar" todir="${dest.dir}/axis2-ws.war/WEB-INF/services">
        </copy>
        <copy todir="${dest.dir}/axis2-ws.war/WEB-INF/classes">
            <fileset dir="${dest.dir}/webservice/tmp">
                <include name="com/**/http/**" />
            </fileset>
        </copy>
        <!--Copy配置文件log4j.properties到classes目录-->
        <copy todir="${dest.dir}/axis2-ws.war/WEB-INF/classes" file="${src.dir}/log4j.properties"></copy>
        <!--Copy用到的JAR包到WEB-INF/lib-->
        <copy todir="${dest.dir}/axis2-ws.war/WEB-INF/lib">
            <fileset dir="${lib.dir}/axis2/">
                <include name="**/**" />
            </fileset>
            <fileset dir="${lib.dir}/log4j/">
                <include name="**/**" />
            </fileset>
            <fileset dir="${lib.dir}/json">
                <include name="**/**"/>
            </fileset>
        </copy>
        <echo message="axis2-ws.aar&#10;" file="${dest.dir}/axis2-ws.war/WEB-INF/services/services.list" append="true" />
        <echo>Create WAR file successfully.</echo>
 
        <delete dir="${dest.dir}/webservice">
        </delete>
    </target>
 
    <target name="clean">
        <delete dir="${dest.dir}">
        </delete>
        <delete file="${jarfile}">
        </delete>
    </target>
 
</project>
{% endhighlight %}

执行AAR任务后，生成的目录结构如下：  
![title](http://h.hiphotos.bdimg.com/album/pic/item/9345d688d43f8794db2f32dbd01b0ef41ad53a76.jpg)
当然了，你甚至可以用Ant将axis2-ws.war复制到tomcat/webapps目录下，并且启动tomcat。

这里还要提一下web.xml，内容如下：
{% highlight xml %}
<?xml version="1.0" encoding="GB2312"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>  
  <servlet>
    <servlet-name>AxisServlet</servlet-name>
    <display-name>Apache-Axis Servlet</display-name>       
    <servlet-class>com.axis2.webservice.http.MyAxisServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>AxisServlet</servlet-name>
    <url-pattern>/services/*</url-pattern>
  </servlet-mapping>
</web-app>
{% endhighlight %}

MyAxisServlet类继承AxisServlet，并重写service方法，你可以在这个方法中做一些拦截，比如过滤非法IP等。
如下：
{% highlight java %}
package com.axis2.webservice.http;
 
import java.io.IOException;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
import org.apache.axis2.transport.http.AxisServlet;
 
public class MyAxisServlet extends AxisServlet{
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res)
            throws ServletException, IOException {
        super.service(req, res);
         
        System.out.println(req.getRemoteAddr());
    }
 
}
{% endhighlight %}

当然，如果你不需要过滤IP，即任何人都可以访问你的webservice，那么web.xml中配置servlet为AxisServlet就可以了。
