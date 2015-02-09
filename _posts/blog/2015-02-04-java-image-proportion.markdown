---
layout: post
title:  Java图片缩放
description: Java图片缩放
date:   2015-02-04 15:28:39
categories: blog
tags: java 图片缩放
---
需求：用户在手机上传一张图片，上传后在后台将图片缩放到750px，如果缩放后的高度>420px，则让用户在页面裁剪高度。  
代码如下：
<pre>
import java.awt.Image;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;

import javax.imageio.ImageIO;

import com.sun.image.codec.jpeg.JPEGCodec;
import com.sun.image.codec.jpeg.JPEGImageEncoder;


public class ImageUtil {

	/**
	 * 如果图片宽度>750，压缩到750；宽度<750，放大到750。
	 * 高度根据比例计算，如果高度>420，由用户在前端裁剪。
	 * @author qincd
	 * @email qincd@hyxt.com
	 * @date Feb 4, 2015 2:45:18 PM
	 */
	public static void main(String[] args) {
		// 原图位置
		// 1)（大图OK），宽度>750，高度>420
		String img = "C:/Users/qince/Pictures/360壁纸 6864.jpg";
		// 2)（小图OK），宽度<750，高度<420
		img = "C:/Users/qince/Pictures/342F5D5568049A306F95F2C18F9964B5.jpg";
		// 3)（640*960 OK）
		img = "C:/Users/qince/Pictures/001.jpg";
		// 4)（780*400 OK）
		img = "C:/Users/Public/Pictures/Sample Pictures/Desert 副本.jpg";
		// 新图位置
		String target = "d:/" + img.substring(img.lastIndexOf("/"));
		try {
			BufferedImage old_image = ImageIO.read(new File(img));
			// 原图宽高
			int old_width = old_image.getWidth();
			int old_height = old_image.getHeight();
			System.out.println("old_width:" + old_width + ",old_height:" + old_height);
			
			// 新图宽高
			int new_width = 750;
			int new_height = 420;
			
			// 计算缩放比例
			double rate = 0f;
			if (old_width > 750) {
				rate = (double)750 / old_width;
			}
			else {
				rate = (double)old_width / 750;
			}
			
			System.out.println("rate:" + rate);
			if (old_width > 750) { // 缩小
				new_height = (int)Math.round(old_height * rate);
			}
			else { // 放大
				new_height = (int)Math.round(old_height / rate);
			}
			
			if (new_height > 420) {
				System.out.println("缩放后的图片高度大于420,，需要前端裁剪高度");
			}
			
			BufferedImage newImage = new BufferedImage(new_width,new_height,BufferedImage.TYPE_INT_RGB);
			newImage.getGraphics().drawImage(old_image.getScaledInstance(new_width, new_height, Image.SCALE_SMOOTH), 0,0,null);
			FileOutputStream fos = new FileOutputStream(target);
			// JPEGImageEncoder可适用于其他图片类型的转换   
            JPEGImageEncoder encoder = JPEGCodec.createJPEGEncoder(fos);   
            encoder.encode(newImage);
            fos.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}
</pre>

更多图片裁剪，缩放请参考：  
[http://www.iteye.com/topic/266585](http://www.iteye.com/topic/266585)  
[http://luoyahu.iteye.com/blog/1312043](http://luoyahu.iteye.com/blog/1312043)  
[http://blog.csdn.net/a673341766/article/details/16827629)(http://blog.csdn.net/a673341766/article/details/16827629)