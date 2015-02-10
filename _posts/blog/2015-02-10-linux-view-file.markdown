---
layout: post
title:  linux查看文件
description: linux查看文件
date:   2015-02-10 20:11:39
categories: blog
tags: linux
---
【cat】
功能：1）显示整个文件。如：$ cat filename
   2）把文件串联接后传到基本输出，如将几个文件合并为一个文件或输出到屏幕。如：$ cat file1 file2 > file                                                            
参数详解：                                                                                                                                                        
-n或-number：由1开始对所有输出的行数编号，如：cat -n filename                                                                                                     
-b或-number-nonblank：和-n相似，只不过对于空白行不编号                                                                                                            
-s或-squeeze-blank：当遇到有连续两行以上的空白行，就代换为一行的空白行                                                                                            
-v或-show-nonprinting                                                                                                                                             
范例：                                                                                                                                                            
cat -n file1 > file2：把file1的档案内容加上行号后输入file2这个档案里                                                                                              
cat -b file1 file2 >> file3：把file1和file2的档案内容加上行号（空白行不加）后将内容附加到file3里                                                                  
cat /dev/null > /etc/test.txt：清空/etc/test.txt档案内容                                                                                                          
cat > filename:创建新文件                                                                                                                                         
                                                                                                                                                                  
【more】                                                                                                                                                          
功能：以百分比的形式查看日志，类似cat，不过会以一页一页的显示方便使用者逐页阅读，                                                                                 
按空白键（space）就往下一页显示，按b键（back）就会往回一页显示，而且还有搜寻字符串的功能（与vi相似），使用中的说明文件，请按h。退出按q。                          
参数详解：                                                                                                                                                        
-d：提示使用者，在画面下方显示[Press space to continue, 'q' to quit.]，如果如果使用者按错键，则会显示 [Press 'h' for instructions.] 而不是 '哔' 声 。             
-s：当遇到有连续两行以上的空白行，就代换成一行的空白行。                                                                                                          
+num：从第num行开始显示                                                                                                                                           
范例：                                                                                                                                                            
more -s file：逐页显示file之档案内容，如有连续两行以上空白行则以一行空白行显示。                                                                                  
more +20 file：从第20行开始显示file之档案内容。                                                                                                                   
【less】                                                                                                                                                          
功能：less命令可以对文件或其他输出进行分页显示，与more命令相似。退出按q。                                                                                         
参数详解：                                                                                                                                                        
-a：在当前屏幕显示最后                                                                                                                                            
-c：从顶部（从上到下）刷新屏幕，并显示文件内容。而不是通过底部滚动完成刷新;                                                                                       
-f：强制打开文件，二进制文件显示时，不提示警告；                                                                                                                  
-i：搜索时忽略大小写；除非搜索串中包含大写字母;                                                                                                                   
-I：搜索时忽略大小写，除非搜索串中包含小写字母;                                                                                                                   
-m：显示当前读取文件的百分比                                                                                                                                      
-M：显示当前读取文件的百分比、行号及总行数；                                                                                                                      
-N：在每行前输出行号                                                                                                                                              
-p pattern:搜索日志文件中含有pattern的所有日志内容；                                                                                                              
-s：把连续多个空白行作为一个空白行显示                                                                                                                            
-Q：在终端下不响铃                                                                                                                                                
扩展：                                                                                                                                                            
U：向上                                                                                                                                                           
J：向下                                                                                                                                                           
g：跳到第一行                                                                                                                                                     
G：跳到最后一行                                                                                                                                                   
/pattern:搜索pattern                                                                                                                                              
q：退出less                                                                                                                                                       
！command：调用SHELL，可以运行命令；比如!ls 显示当前列当前目录下的所有文件;                                                                                       
【head】                                                                                                                                                          
功能：从文本文件的头部开始查看，head命令用于查看一个文本文件的开头部分。                                                                                          
参数详解：                                                                                                                                                        
-n 制定您想要显示文本多少行                                                                                                                                       
-n number 这个参数选项必须是十进制的整数，它将决定在文件中的位置，以字节单位。                                                                                    
-c number 这个参数选项必须是十进制的整数，它将决定在文件中的位置，以字节为单位。                                                                                  
范例：                                                                                                                                                            
head file.txt 显示file.txt的前十行内容；                                                                                                                          
head -n 20 file.txt 显示file.txt的前二十行内容；                                                                                                                  
【tail】                                                                                                                                                          
功能：用于显示文本文件的末尾几行。                                                                                                                                
参数详解：                                                                                                                                                        
-b Number  ：从Number变量表示的512自己块位置开始读取制定文件。                                                                                                    
-c Number  ：从Number变量表示的字节位置开始读取指定文件。                                                                                                         
-f         ：tail -f 命令可用于监视另一个进程正在写入的文件的增长。                                                                                               
-k Number  ：从Number变量表示的1KB块位置开始读取制定文件。                                                                                                        
-m Number  ：从Number变量表示的多字节字符位置开始读取指定文件。使用该标志提供在单字节和双字节字符代码集环境中的一致结果。                                         
-n Number  ：从首行或末行位置来读取指定文件，位置由 Number 变量的符号（+ 或 - 或无）表示，并通过行号 Number 进行位移。                                            
范例：                                                                                                                                                            
tail file.txt :显示文件file.txt的后十行内容。                                                                                                                     
tail -n 20 file.txt :显示文件file.txt的后二十行内容。                                                                                                             
tail -f file.txt ：显示文件file.txt的后十行内容并在文件内容增加后，自动显示新增的文件内容。                                                                       
tail -n 50 -f file.txt ：显示文件file.txt的后50行内容并在文件内容增加后，自动显示新增内容。                                                                       