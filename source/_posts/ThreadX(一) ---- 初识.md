---
title: ThreadX(一) ------ 初识 # 文章标题  
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(一) ------ 初识
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-07-28 22:04:56
---

## 介绍

<b>
	Azure RTOS ThreadX是专为嵌入式应用程序设计的高性能实时内核。
与其他实时内核不同，ThreadX具有通用性-通过使用功能强大的CISC，RISC和DSP处理器的应用程序，可以轻松地在基于微控制器的小型应用程序中扩展。
<br>ThreadX可基于其基础体系结构进行扩展。因为ThreadX服务是作为C库实现的，所以只有应用程序实际使用的那些服务才被带到运行时映像中。 因此，ThreadX的实际大小完全由应用程序确定。 对于大多数应用程序，ThreadX的指令映像大小在2 KB至15 KB之间。
</b>

## 启动过程

![](https://img-blog.csdnimg.cn/20200818104442474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
## 线程状态转换
![](https://img-blog.csdnimg.cn/20200812185340866.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_30,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_20,color_0000ee,t_100#pic_center)


## 数据类型
![](https://img-blog.csdnimg.cn/20200812182149283.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

```c
#define VOID                                    void
typedef char                                    CHAR;
typedef unsigned char                           UCHAR;
typedef int                                     INT;
typedef unsigned int                            UINT;
typedef long                                    LONG;
typedef unsigned long                           ULONG;
typedef short                                   SHORT;
typedef unsigned short                          USHORT;
```
## FLASH & RAM
![](https://img-blog.csdnimg.cn/20200812191129136.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)


## 源码获取
```bash
git clone https://github.com/azure-rtos/threadx.git
```

[API传送门](https://docs.microsoft.com/en-us/azure/rtos/threadx/appendix-a#entry-function)
