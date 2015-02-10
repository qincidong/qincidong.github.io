---
layout: post
title:  Tomcat5.5(6.0)配置-多域名绑定和虚拟目录
description: Tomcat5.5(6.0)配置-多域名绑定和虚拟目录
date:   2015-02-10 20:11:39
categories: blog
tags: tomcat 
---
Tomcat5.5 (6.0)配置-多域名绑定和虚拟目录  
需要修改配置文件:Tomcat 5.5(6.0)\conf\server.xml  
server.xml 的修改方式如下:  

1.如果你要绑定网站,首先是要把tomcat的默认访问端口8080,修改成80  
原始:
<pre>
<Connector port="8080" maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" />
</pre>
修改后:
<pre>
<Connector port="80" maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" />
</pre>
其实这里就是把port:8080,修改成port:80就可以了,其他的参数不变  

2.重点配置部分
原始:  
<pre>
<Engine >Catalina" defaultHost="localhost">
<Host >localhost" appBase="webapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
</Engine>
</pre>

当然这里我把注释代码统统删除掉了,省的碍眼  
修改后:  
<pre>
<Engine >Catalina" defaultHost="www.abc>
<Host appBase=="abcapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false"
<Host >www.cba.com appBase=="D:\cba" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
<Host >localhost" appBase="webapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
</Engine>
</pre>

这里解释一下上面的配置  
Engine 的 dafaultHost :表示访问该tomcat默认进入的主机,注意一定不能是localhost,不然别人通过你的ip访问,就会默认进入tomcat的管理界面.   在没有指定defualtHost的情况下还有另外一种方式指定：  
Tomcat5.5\conf\Catalina\对应的域名\ROOT.xml  
这样也可以指定虚拟目录，配置内容如下：  
<pre>
<Context path="/" docBase="${catalina.home}/portal" debug="5" reloadable="true" crossContext="true">
</Context>
</pre>

Host 的 name:表示该主机绑定的域名,如果绑定localhost则可以通过在浏览器中输入localhost访问该Host.  
Host的 appBase:表示该主机绑定的文件存放路径,可以使用相对路径或绝对路径.  
按照上面的配置:  
1.如果我在浏览器中输入 则访问 Tomcat 5.5\webapps\ROOT 下的网站  
2.如果输入 则访问 Tomcat 5.5\abcapps\ROOT 下的网站  
3.如果输入 则访问 D:\cba\ROOT 下的网站.  
注意这里有一个ROOT目录需要创建,我们只要把网站放到相应的ROOT目录向下面,即可通过相应域名访问了.  
这里面的参数还有很多,我也不是很清楚,不过这样做确实可以实现多域名绑定哈哈.而且网站页面修改了只要直接覆盖就可以了,tomcat可以自动更新类和页面,当然如果修改了web.xml或lib,则需要重启tomcat才可以.  
<pre>
<Host appBase="webapps"
        unpackWARs="true" autoDeploy="true"
        xmlValidation="false" xmlNamespaceAware="false">
<Context path="/cqq" docBase="f:\java\cqqapp" debug="0" reloadable="true" crossContext="true"> 
   </Host>
</pre>
   
Host 标记是用来配置虚拟主机的，就是可以多个域名指向一个tomcat，格式只要参考默认的就可以了。   
context 是Host标记的子元素吧，表示一个虚拟目录，它主要有两个属性，path就相当于虚拟目录名字，   
而 docbase则是具体的文件位置，可以使用相对目录${catalina.home} ，表示tomcat安装目录。在这里我的虚拟路径名称为cqq，实际上我的程序也就是html、jsp、   
servlet都 放在了f:\java\cqqapp这个目录下了。   
这样我就可以通过 http://127.0.0.1/cqq/ 访问我的这个虚拟目录了。  
===========================================================================  
完整配置文档如下，这样在输入域名后（）就可以直接访问你 Innovate项目了  
<pre>
    <Engine defaultHost="www.ww.org.cn">
     <Host debug="0" appBase="webapps"
            unpackWARs="true" autoDeploy="true"
            xmlValidation="false" xmlNamespaceAware="false">
            <Context path="" docBase="D:\tomcat\webapps\Innovate" degug="0" reloadable="true"     crossContext="true"></Context>
      </Host>
    </Engine>
</pre>    
8月7日Tomcat5.5 (6.0)配置-多域名绑定和虚拟目录  
需要修改配置文件:Tomcat 5.5(6.0)\conf\server.xml  
server.xml 的修改方式如下:  
1.如果你要绑定网站,首先是要把tomcat的默认访问端口8080,修改成80  
原始:  
<pre>
<Connector port="8080" maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" />
</pre>

修改后:  
<pre>
<Connector port="80" maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" />
</pre>

其实这里就是把port:8080,修改成port:80就可以了,其他的参数不变  


2.重点配置部分  
原始:  
<pre>
<Engine >Catalina" defaultHost="localhost">
<Host >localhost" appBase="webapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
</Engine>
</pre>

当然这里我把注释代码统统删除掉了,省的碍眼  
修改后:  
<pre>
<Engine >Catalina" defaultHost="www.abc>
<Host appBase=="abcapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false"
<Host >www.cba.com appBase=="D:\cba" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
<Host >localhost" appBase="webapps" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false" />
</Engine>
</pre>

这里解释一下上面的配置  
Engine 的 dafaultHost :表示访问该tomcat默认进入的主机,注意一定不能是localhost,不然别人通过你的ip访问,就会默认进入tomcat的管理界面.   在没有指定defualtHost的情况下还有另外一种方式指定：  
Tomcat5.5\conf\Catalina\对应的域名\ROOT.xml  
这样也可以指定虚拟目录，配置内容如下：  
<pre>
<Context path="/" docBase="${catalina.home}/portal" debug="5" reloadable="true" crossContext="true">
</Context>
</pre>

Host 的 name:表示该主机绑定的域名,如果绑定localhost则可以通过在浏览器中输入localhost访问该Host.  
Host的 appBase:表示该主机绑定的文件存放路径,可以使用相对路径或绝对路径.  
按照上面的配置:  
1.如果我在浏览器中输入 则访问 Tomcat 5.5\webapps\ROOT 下的网站  
2.如果输入 则访问 Tomcat 5.5\abcapps\ROOT 下的网站  
3.如果输入 则访问 D:\cba\ROOT 下的网站.  
注意这里有一个ROOT目录需要创建,我们只要把网站放到相应的ROOT目录向下面,即可通过相应域名访问了.  
这里面的参数还有很多,我也不是很清楚,不过这样做确实可以实现多域名绑定哈哈.而且网站页面修改了只要直接覆盖就可以了,tomcat可以自动更新类和页面,当然如果修改了web.xml或lib,则需要重启tomcat才可以.  
<pre>
<Host appBase="webapps"
        unpackWARs="true" autoDeploy="true"
        xmlValidation="false" xmlNamespaceAware="false">
<Context path="/cqq" docBase="f:\java\cqqapp" debug="0" reloadable="true" crossContext="true"> 
   </Host>
</pre>
   
Host 标记是用来配置虚拟主机的，就是可以多个域名指向一个tomcat，格式只要参考默认的就可以了。   
context 是Host标记的子元素吧，表示一个虚拟目录，它主要有两个属性，path就相当于虚拟目录名字，   
而 docbase则是具体的文件位置，可以使用相对目录${catalina.home} ，表示tomcat安装目录。在这里我的虚拟路径名称为cqq，实际上我的程序也就是html、jsp、   
servlet都 放在了f:\java\cqqapp这个目录下了。   
这样我就可以通过 http://127.0.0.1/cqq/ 访问我的这个虚拟目录了。  
===========================================================================  
完整配置文档如下，这样在输入域名后（）就可以直接访问你 Innovate项目了  
</pre>
    <Engine defaultHost="www.ww.org.cn">
     <Host debug="0" appBase="webapps"
            unpackWARs="true" autoDeploy="true"
            xmlValidation="false" xmlNamespaceAware="false">
            <Context path="" docBase="D:\tomcat\webapps\Innovate" degug="0" reloadable="true"     crossContext="true"></Context>
      </Host>
    </Engine>
</pre>    