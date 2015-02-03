---
layout: post
title:  获取项目的路径
description: 获取项目的路径
date:   2015-02-03 14:17:39
categories: blog
tags: 
---
参考网络文章整理
1.  获取web服务器项目的真实物理路径
request.getSession().getServletContext().getRealPath("/");

2.  相对路径的获得
说明:相对路径(即不写明时候到底相对谁)均可通过以下方式获得（不论是一般的java项目还是web项目）

String relativelyPath=System.getProperty("user.dir");

上述相对路径中，java项目中的文件是相对于项目的根目录

web项目中的文件路径视不同的web服务器不同而不同

（tomcat是相对于 tomcat安装目录\bin）

3.  类加载目录的获得(即当运行时某一类时获得其装载目录)
3.1. 通用的方法一
(不论是一般的java项目还是web项目,先定位到能看到包路径的第一级目录) 　　

　　 InputStream is=TestAction.class.getClassLoader().getResourceAsStream("test.txt");

　　 (test.txt文件的路径为项目名\src\test.txt;类TestAction所在包的第一级目录位于src目录下) 　　

　　 上式中将TestAction，test.txt替换成对应成相应的类名和文件名字即可

3.2. 通用方法二 (此方法和1.1中的方法类似,不同的是此方法必须以'/'开头)
　　 InputStream is=Test1.class.getResourceAsStream("/test.txt");

　　 (test.txt文件的路径为项目名\src\test.txt,类Test1所在包的第一级目录位于src目录下) 　　

4.  web项目根目录的获得(发布之后)
4.1. 从servlet出发
　　 可建立一个servlet在其的init方法中写入如下语句

　　 ServletContext s1=this.getServletContext();

　　 String temp=s1.getRealPath("/"); (关键)

　　 结果形如：D:\工具\Tomcat-6.0\webapps\002_ext\ (002_ext为项目名字)

　　 如果是调用了s1.getRealPath("")则输出D:\工具\Tomcat-6.0\webapps\002_ext(少了一个"\")

4.2. 从httpServletRequest出发
　　 String cp11111=request.getSession().getServletContext().getRealPath("/");

 

　　 结果形如:D:\工具\Tomcat-6.0\webapps\002_ext\

5.  classpath的获取
(在Eclipse中为获得src或者classes目录的路径)

5.1. 方法一
       Thread.currentThread().getContextClassLoader().getResource("").getPath();

       eg:

　　 String t=Thread.currentThread().getContextClassLoader().getResource("").getPath();

　　 System.out.println("t---"+t);

　　 输出:t---/E:/order/002_ext/WebRoot/WEB-INF/classes/ 　　

5.2. 方法二
JdomParse.class.getClassLoader().getResource("").getPath() (JdomParse为src某一个包中的类,下同)

　　 eg:

String p1=JdomParse.class.getClassLoader().getResource("").getPath();

System.out.println("JdomParse.class.getClassLoader().getResource--"+p1);

输出:

JdomParse.class.getClassLoader().getResource--/E:/order/002_ext/WebRoot/WEB-INF/classes/

另外,如果想把文件放在某一包中,则可以通过以下方式获得到文件(先定位到该包的最后一级目录)

eg

String p2=JdomParse.class.getResource("").getPath();

System.out.println("JdomParse.class.getResource---"+p2);

输出:

JdomParse.class.getResource---/E:/order/002_ext/WebRoot/WEB-INF/classes/jdom/

(JdomParse为src目录下jdom包中的类)

在jsp和class文件中调用的相对路径不同。 在jsp里，根目录是WebRoot 在class文件中，根目录是WebRoot/WEB-INF/classes 当然你也可以用System.getProperty("user.dir")获取你工程的绝对路径。

6.  在Jsp,Servlet,Java中详细获得路径的方法
6.1. jsp中取得路径：
以工程名为TEST为例：

(1)得到包含工程名的当前页面全路径：request.getRequestURI()

结果：/TEST/test.jsp

(2)得到工程名：request.getContextPath()

结果：/TEST

(3)得到当前页面所在目录下全名称：request.getServletPath()

结果：如果页面在jsp目录下 /TEST/jsp/test.jsp

(4)得到页面所在服务器的全路径：application.getRealPath("页面.jsp")

结果：D:\resin\webapps\TEST\test.jsp

(5)得到页面所在服务器的绝对路径：absPath=new java.io.File(application.getRealPath(request.getRequestURI())).getParent();

结果：D:\resin\webapps\TEST

6.2. 在类中取得路径：
(1)类的绝对路径：Class.class.getClass().getResource("/").getPath()

结果：/D:/TEST/WebRoot/WEB-INF/classes/pack/

(2)得到工程的路径：System.getProperty("user.dir")

结果：D:\TEST

6.3. 在Servlet中取得路径：
(1)得到工程目录：request.getSession().getServletContext().getRealPath("") 参数可具体到包名。

结果：E:\Tomcat\webapps\TEST

(2)得到IE地址栏地址：request.getRequestURL()

结果：http://localhost:8080/TEST/test

(3)得到相对地址：request.getRequestURI()

结果：/TEST/test