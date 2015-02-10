---
layout: post
title:  在同一个tomcat下，不同的web项目共享session
description: 在同一个tomcat下，不同的web项目共享session
date:   2015-02-10 20:11:39
categories: blog
tags: tomcat session
---
有时我们会遇到一个大点的项目，为了方便实现有些功能，我们会把项目拆成不同的独立web项目。  
但我们在管理这些项目时，只有一个登陆口，然后在其他项目取session来实现身份的验证。  
1、配置tomcat下 conf/server.xml文件，在<Host></Hoser>配置如下：  
{% highlight xml %}
<Host name="localhost"  appBase="webapps" 
    unpackWARs="true" autoDeploy="true" 
    xmlValidation="false" xmlNamespaceAware="false">
    <!-- aa 和bb 为两个不同的web项目  -->
    <Context path="/aa"  reloadable="true" crossContext="true" /> 
    <Context path="/bb"  reloadable="true" crossContext="true"/>
</Host>
{% endhighlight %}

2、 在 aa项目存入session
{% highlight java %}
session.setAttribute("user",userInfo); 
ServletContext ContextA =session.getServletContext(); 
ContextA.setAttribute("session", session);
{% endhighlight %}

3、在bb 项目取出 session
{% highlight java %}
HttpSession session1 =request.getSession();   
ServletContext Context = session1.getServletContext();   
ServletContext Context1= Context.getContext("/aa");     //项目A名
if(Context1 !=null && !Context1.equals("")){       
    HttpSession sess =(HttpSession)Context1.getAttribute("session"); 
    out.println(sess.getAttribute("user")); 
}
{% endhighlight %}