---
layout: post
title:  使用jquery jcrop插件进行图片的截取与保存
description: 使用jquery jcrop插件进行图片的截取与保存
date:   2015-02-05 14:28:39
categories: blog
tags: java 图片缩放 jquery jcrop
---
第一步：上传大图片到服务器，并且展示到页面  
第二部：调用jcrop组件js及css  
{% highlight javascript %}
<script src="${ctx}/js/jquery-jcrop/jquery.Jcrop.js"></script>    
<link rel="stylesheet" href="${ctx}/css/jquery-jcrop/jquery.Jcrop.css" type="text/css" />  
{% endhighlight %}

第三部：采用插件  
进行图片的选取，其中主要包括x,y,width,height  
{% highlight javascript %}
$('#cropButton').click(function(){  
    var x = $("#x").val();  
    var y = $("#y").val();  
    var w = $("#w").val();  
    var h = $("#h").val();    
    if(w == 0 || h == 0 ){  
        alert("您还没有选择图片的剪切区域,不能进行剪切图片!");  
        return;  
    }     
    //alert("你要剪切图片的X坐标: "+x + ",Y坐标: " + y + ",剪切图片的宽度: " + w + ",高度：" + h );  
    if(confirm("确定按照当前大小剪切图片吗")){                 
        //document.form1.submit();  
        //$("#makeHeadImgDiv").toggle();  
        function loadHeadUrl(data){  
            //alert(data.headUrl);  
             alert(data.retMsg);  
             $("#img_headUrl").attr("src",data.headUrl);  
             $("#headUrl").val(data.headUrl);  
            //alert($("#headUrl").val());  
        }  
        var url="${ctx}/admin/pri/guest/guest_cutHeadImg.action";  
        var options ={   
                //beforeSubmit: validate,  
                url:       url,  
                success:   loadHeadUrl,   
                type:      'post',        
                dataType:  'json'  
        };  
        $('#cutHeadImgFrm').ajaxSubmit(options);  
    }  
});  
{% endhighlight %}     


然后调用后台方法切割图片。                
设计的包：                                
import java.awt.*;                        
import java.awt.image.*;                  
import java.awt.Graphics;                 
import java.awt.color.ColorSpace;         
import javax.imageio.ImageIO;             
第四部：保存新文件路径，展示图片到页面。  
{% highlight java %}
package com.wondertek.meeting.util;  
  
import java.io.*;  
import java.awt.*;  
import java.awt.image.*;  
import java.awt.Graphics;  
import java.awt.color.ColorSpace;  
import javax.imageio.ImageIO;  
  
public class ImageCut {  
      
    /**  
     * 图像切割（改）     *  
     * @param srcImageFile            源图像地址 
     * @param dirImageFile            新图像地址 
     * @param x                       目标切片起点x坐标 
     * @param y                      目标切片起点y坐标 
     * @param destWidth              目标切片宽度 
     * @param destHeight             目标切片高度 
     */  
    public static void abscut(String srcImageFile,String dirImageFile, int x, int y, int destWidth,  
            int destHeight) {  
        try {  
            Image img;  
            ImageFilter cropFilter;  
            // 读取源图像  
            BufferedImage bi = ImageIO.read(new File(srcImageFile));  
            int srcWidth = bi.getWidth(); // 源图宽度  
            int srcHeight = bi.getHeight(); // 源图高度            
            if (srcWidth >= destWidth && srcHeight >= destHeight) {  
                Image image = bi.getScaledInstance(srcWidth, srcHeight,  
                        Image.SCALE_DEFAULT);  
                // 改进的想法:是否可用多线程加快切割速度  
                // 四个参数分别为图像起点坐标和宽高  
                // 即: CropImageFilter(int x,int y,int width,int height)  
                cropFilter = new CropImageFilter(x, y, destWidth, destHeight);  
                img = Toolkit.getDefaultToolkit().createImage(  
                        new FilteredImageSource(image.getSource(), cropFilter));  
                BufferedImage tag = new BufferedImage(destWidth, destHeight,  
                        BufferedImage.TYPE_INT_RGB);  
                Graphics g = tag.getGraphics();  
                g.drawImage(img, 0, 0, null); // 绘制缩小后的图  
                g.dispose();  
                // 输出为文件  
                ImageIO.write(tag, "JPEG", new File(dirImageFile));  
            }  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
      
    /** 
     * 缩放图像 
     *  
     * @param srcImageFile       源图像文件地址 
     * @param result             缩放后的图像地址 
     * @param scale              缩放比例 
     * @param flag               缩放选择:true 放大; false 缩小; 
     */  
    public static void scale(String srcImageFile, String result, int scale,  
            boolean flag) {  
        try {  
            BufferedImage src = ImageIO.read(new File(srcImageFile)); // 读入文件  
            int width = src.getWidth(); // 得到源图宽  
            int height = src.getHeight(); // 得到源图长  
            if (flag) {  
                // 放大  
                width = width * scale;  
                height = height * scale;  
            } else {  
                // 缩小  
                width = width / scale;  
                height = height / scale;  
            }  
            Image image = src.getScaledInstance(width, height,Image.SCALE_DEFAULT);  
            BufferedImage tag = new BufferedImage(width, height,BufferedImage.TYPE_INT_RGB);  
            Graphics g = tag.getGraphics();  
            g.drawImage(image, 0, 0, null); // 绘制缩小后的图  
            g.dispose();  
            ImageIO.write(tag, "JPEG", new File(result));// 输出到文件流  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
      
    /** 
     * 重新生成按指定宽度和高度的图像 
     * @param srcImageFile       源图像文件地址 
     * @param result             新的图像地址 
     * @param _width             设置新的图像宽度 
     * @param _height            设置新的图像高度 
     */  
    public static void scale(String srcImageFile, String result, int _width,int _height) {        
        scale(srcImageFile,result,_width,_height,0,0);  
    }  
      
    public static void scale(String srcImageFile, String result, int _width,int _height,int x,int y) {  
        try {  
              
            BufferedImage src = ImageIO.read(new File(srcImageFile)); // 读入文件  
              
            int width = src.getWidth(); // 得到源图宽  
            int height = src.getHeight(); // 得到源图长  
              
            if (width > _width) {  
                 width = _width;  
            }  
            if (height > _height) {  
                height = _height;  
            }             
            Image image = src.getScaledInstance(width, height,Image.SCALE_DEFAULT);  
            BufferedImage tag = new BufferedImage(width, height,BufferedImage.TYPE_INT_RGB);  
            Graphics g = tag.getGraphics();  
            g.drawImage(image, x, y, null); // 绘制缩小后的图  
            g.dispose();              
            ImageIO.write(tag, "JPEG", new File(result));// 输出到文件流  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
      
    /** 
     * 图像类型转换 GIF->JPG GIF->PNG PNG->JPG PNG->GIF(X) 
     */  
    public static void convert(String source, String result) {  
        try {  
            File f = new File(source);  
            f.canRead();  
            f.canWrite();  
            BufferedImage src = ImageIO.read(f);  
            ImageIO.write(src, "JPG", new File(result));  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
    /** 
     * 彩色转为黑白 
     *  
     * @param source 
     * @param result 
     */  
    public static void gray(String source, String result) {  
        try {  
            BufferedImage src = ImageIO.read(new File(source));  
            ColorSpace cs = ColorSpace.getInstance(ColorSpace.CS_GRAY);  
            ColorConvertOp op = new ColorConvertOp(cs, null);  
            src = op.filter(src, null);  
            ImageIO.write(src, "JPEG", new File(result));  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
  
    /** 
     * @param args 
     */  
    public static void main(String[] args) {  
        //晕。。。搞成多个了...  
        //cut("c:/images/ipomoea.jpg", "c:/images/t/ipomoea.jpg", 200, 150);  
        //ok  
        //gray("c:/images/ipomoea.jpg", "c:/images/t/ipomoea.jpg");  
        //convert("c:/images/ipomoea.jpg", "c:/images/t/ipomoea.gif");  
        //scale("c:/images/5105049910001020110718648723.jpg", "c:/images/t/5105049910001020110718648725.jpg",154,166,157,208);  
        //scale("c:/images/rose1.jpg", "c:/images/t/rose1.jpg",154,166,157,208);  
        scale("c:/images/rose1.jpg", "c:/images/t/rose2.jpg",154,166,10,10);  
          
    }  
}  
{% endhighlight %}

转载：[http://blog.csdn.net/guoxu775/article/details/7905065](http://blog.csdn.net/guoxu775/article/details/7905065)
