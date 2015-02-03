---
layout: post
title:  jQuery AjaxFileUpload插件使用
description: 使用jQuery AjaxFileUpload插件无刷新上传文件
date:   2015-02-03 14:17:39
categories: blog
tags: jQuery ajaxFileUpload 文件上传
---
AjaxFileUpload官网：http://www.phpletter.com/Our-Projects/AjaxFileUpload/  
有使用示例，插件下载在页面的最下方“Where to download”那里。  

前端页面的示例代码就不说了，官网已经写的很清楚。下面直接上代码。    
实现的功能时，用户选择一个文件，然后点击上传按钮，上传成功后页面显示上传的图片。  
页面：
{% highlight jsp %}
<%@ page language="java" pageEncoding="UTF-8"%>
<%@include file="/commons/taglibs.jsp" %>    
<!DOCTYPE html>
<html>
<head>
<title>AjaxFileUpload Demo</title>
	<script type="text/javascript" src="${root}/scripts/jquery.js"></script>	
	<script type="text/javascript" src="${root}/scripts/ajaxfileupload.js"></script>	
	<script type="text/javascript">
		$(function(){
			$('#loading').ajaxStart(function(){
				$(this).show();
			}).ajaxComplete(function(){
				$(this).hide();
			});
			
			$('#uploadBtn').click(function(){
				if ($.trim($('#file1').val()).length > 0) {
					$.ajaxFileUpload(
					{
						url:'${root}/ajaxFileUpload/upload',
						secureuri:false,
						fileElementId:'file1',
						dataType:'json', // 返回的结果被包装在<pre>标签中，报语法错误。
						data:{ // 额外的数据
							ts:new Date().getTime()
						},
						success:function(data,status){
							if(typeof(data.error) != 'undefined') {
								 if(data.error != '') {
								  alert('erorr:' + data.error);
								 }
								 else {
								  alert(data.msg);
								  $('#preview').attr('src',data.savePath).show();									  
								 }
							}
						},
						error:function(data,status,e) {
							alert(e);
						}
					}
					);
				}
				else {
					alert("请选择文件");
				}
				
				return false;
			});
		});
	</script>
</head>
<body>
	<div id="loading" style="display:none;">文件正在上传，请稍后...</div>
	<div>
		<input type="file" id="file1" name="file"><input type="button" value="Upload" id="uploadBtn"><br>
		<img id='preview' src='' style='display:none;width:50%;height:50%;'>
	</div>
</body>
</html>
{% endhighlight %}

后台上传代码：
{% highlight java %}
package com.company.project.controller;

import java.io.File;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Random;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javacommon.base.BaseRestSpringController;

@Controller
@RequestMapping(value="/ajaxFileUpload")
public class AjaxFileUploadController extends BaseRestSpringController<Object, Object>{

	@RequestMapping(value="/upload")
	@ResponseBody
	public Map<String, String> uplaod(HttpServletRequest request) {
		Map<String,String> map = new HashMap<String, String>();
		map.put("error", "");
		map.put("msg", "上传成功！");
		
		DiskFileItemFactory dfif = new DiskFileItemFactory();
		// 设置缓存大小，如果文件大小超过缓存大小，将使用临时目录作为缓存
		dfif.setSizeThreshold(1024*1024);
		// 保存到项目决定位置下的upload文件夹，比如：d:/tomcat/webapps/rapiddemo/upload/xxxxxxxxx.jpg
		String webRoot = request.getSession().getServletContext().getRealPath("/") + "upload/";
		// 项目相对位置（用于在页面显示），比如：/rapiddemo/upload/xxxxxxxxx.jpg
		String webRootRelative = request.getContextPath() + "/upload/";
		File uploadDir = new File(webRoot);
		if (!uploadDir.exists()) {
			uploadDir.mkdirs();
		}
		
		String ts = null;
		ServletFileUpload sfu = new ServletFileUpload(dfif);
		try {
			List<FileItem> fileItemList = sfu.parseRequest(request);
			for (int i=0;i<fileItemList.size();i++) {
				FileItem fi = fileItemList.get(i);
				if (fi.isFormField()) {
					String filedName = fi.getFieldName();
					String filedValue = fi.getString();
					System.out.println(filedName + ":" + filedValue);
					
					if (filedName.equals("ts")) {
						ts = filedValue;
					}
				}
				else {
					// 文件
					String fileNameFullPath = fi.getName();
					// 取文件后缀
					String fileExt = fileNameFullPath.substring(fileNameFullPath.lastIndexOf("."));
					// 随机文件名：yyyymmddhhmiss+3位随机数
					String fileName = new SimpleDateFormat("yyyyMMddhhmmss").format(new Date()) + new Random().nextInt(1000) + fileExt;
					fi.write(new File(webRoot + fileName));
					System.out.println("文件【" + fileNameFullPath + "】上传成功！\n路径：" + (webRoot + fileName));
					map.put("savePath",(webRootRelative + fileName));
					System.out.println("文件上传时间：" + ts);
				}
			}
		} catch (FileUploadException e) {
			map.put("error", "上传失败：" + e.getMessage());
			e.printStackTrace();
		} catch (Exception e) {
			map.put("error", "上传失败：" + e.getMessage());
			e.printStackTrace();
		}
		return map;
	}
}
{% endhighlight %}

但是这样呢，有一个问题，文件是上传成功了，但是页面提示语法错误。如下：  
SyntaxError：unexpected token <  

将dataType改成content，alert data的内容，可以看到返回的json被pre标签包裹，所以出现了这个错误。  

可以通过修改ajaxfileupload.js来解决这个问题。找到uploadHttpData方法定义的地方，将type = json时的处理逻辑改一下，去掉>。  
增加如下代码：  
{% highlight javascript%}
////////////add code begin///////////////   
 data = r.responseText;   
 var start = data.indexOf(">");   
 if(start != -1) {   
   var end = data.indexOf("<", start + 1);   
   if(end != -1) {   
     data = data.substring(start + 1, end);   
    }   
 }   
  ///////////add code end///////////////   
{% endhighlight %}              

关于多文件上传，可以参考：[http://blog.csdn.net/zhanglu201112/article/details/17039137](http://blog.csdn.net/zhanglu201112/article/details/17039137)