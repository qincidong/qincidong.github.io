---
layout: post
title:  Axis2 Webservice之文件上传
description: Axis2 Webservice之文件上传
date:   2015-02-10 20:11:39
categories: blog
tags: axis2 webservice
---
有2种方式：  
第一种方式是传递byte[]。  
第二种方式是使用DataHandler。  

第一种：
Webservice类：
{% highlight java %}
package com.axis2.webservice;
 
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
 
import javax.activation.DataHandler;
 
import org.apache.log4j.Logger;
 
public class MyService {
    public final Logger logger = Logger.getLogger(MyService.class);
    public boolean uploadImage(byte[] data) {
        boolean uploadFlag = false;
        if (data != null && data.length > 0) {
            FileOutputStream fos = null;
            try {
                String filename = "d:/test.jpg";
                fos = new FileOutputStream(filename);
                fos.write(data);
                fos.close();
                uploadFlag = true;
                logger.debug("文件上传成功。。。");
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fos != null) {
                    try {
                        fos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
        return uploadFlag;
    }
}
{% endhighlight %}

客户端：

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
             
            // 上传文件
            FileInputStream fis = null;
            try {
                 File file = new File("D:/Users/ex-qincidong001/Pictures/u=848855780,3658838396&fm=21&gp=0.jpg");
                 byte[] data = new byte[(int) file.length()];
                 fis = new FileInputStream(file);
                 fis.read(data);
                 QName qName = new QName("http://webservice.axis2.com","uploadImage");
                 boolean uploadFlag = (Boolean)serviceClient.invokeBlocking(qName, new Object[]{data}, new Class[]{Boolean.class})[0];
                 System.out.println(uploadFlag);
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fis != null)
                    try {
                        fis.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
            }
             
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
 
}
{% endhighlight %}


第二种方式：  
Webservice类：  
{% highlight java %}
package com.axis2.webservice;
 
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
 
import javax.activation.DataHandler;
 
import org.apache.log4j.Logger;
 
public class MyService {
    public final Logger logger = Logger.getLogger(MyService.class);
 
    public boolean uploadFile(DataHandler dh,String filename) {
        if (dh != null) {
            FileOutputStream fos = null;
            InputStream is = null;
            try {
                 fos = new FileOutputStream("d:/"+filename);
                 is = dh.getInputStream();
                byte[] data = new byte[1024];
                int len = -1;
                 
                while ((len = is.read(data)) != -1) {
                    fos.write(data, 0, len);
                }
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fos != null) {
                    try {
                        fos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                if (is != null) {
                    try {
                        is.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
            return true;
        }
        return false;
    }
}
{% endhighlight %}

客户端：  
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
 
            // 使用DataHandler处理文件上传
            QName qName = new QName("http://webservice.axis2.com","uploadFile");
            try {
                // 或者DataHandler = new DataHandler(new FileDataSource(filename));
                DataHandler dh = new DataHandler(new URL("设计模式09-门面模式.pdf"));
                String filename = "设计模式09-门面模式.pdf";
                boolean uploadFlag = (Boolean) serviceClient.invokeBlocking(qName, new Object[]{dh,filename}, new Class[]{Boolean.class})[0];
                System.out.println(uploadFlag);
            } catch (MalformedURLException e) {
                e.printStackTrace();
            }
        } catch (AxisFault e) {
            e.printStackTrace();
        }
    }
 
}
{% endhighlight %}