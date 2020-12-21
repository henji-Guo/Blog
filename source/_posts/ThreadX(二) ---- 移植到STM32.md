---
title: ThreadX(二) ------ 移植到STM32 # 文章标题  
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(二) ------ 移植到STM32
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020/08/13 20:54:11
---

## 新建裸机项目

- 目录结构
![](https://img-blog.csdnimg.cn/20200812193339863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 主函数

```c
int main(void)
{
  /* USER CODE BEGIN 1 */
	uint8_t pData[]="=========ThreadX========= ";
  /* USER CODE END 1 */
  .......
  .......
  .......	
  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
	  HAL_UART_Transmit(&huart1, pData, sizeof(pData), HAL_MAX_DELAY);
	  HAL_Delay(1000);
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}
```
![](https://img-blog.csdnimg.cn/20200812193533578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

## ThreadX 源码

```bash
git clone https://github.com/azure-rtos/threadx.git
```
![](https://img-blog.csdnimg.cn/20200812194627241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 主要的两个文件

文件夹|内容
----- | ---- 
common | 源码
ports | 移植文件

![](https://img-blog.csdnimg.cn/20200812194730685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
找到`threadx\ports\cortex_m4\gnu\example_build`
把tx_initialize_low_level.S文件复制到`\threadx\ports\cortex_m4\gnu\src`
![](https://img-blog.csdnimg.cn/20200812195501334.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812195800662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
* 回到STM32CubeIDE reflesh 工程

![](https://img-blog.csdnimg.cn/20200812200354549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 开始添加编译

![](https://img-blog.csdnimg.cn/20200812200447459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812200609753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812200704374.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/2020081220073894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 添加的两个头文件路径![](https://img-blog.csdnimg.cn/20200812200822367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812201017871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812201051992.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 添加上的源码路径
![](https://img-blog.csdnimg.cn/20200812201241300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

## 修改文件

![](https://img-blog.csdnimg.cn/20200812201448739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
不出意外肯定是会报错的（移植怎么可能什么都不改是吧）
![](https://img-blog.csdnimg.cn/2020081220180646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
看下具体的错误，啊，原来是重复定义了，被threadx接管了，那么只要把它注释掉在编译。
![](https://img-blog.csdnimg.cn/20200812201846305.png#pic_center)
![](https://img-blog.csdnimg.cn/20200812202126977.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
果不其然 error 又出现了（哪有注释几行就完事了）
![](https://img-blog.csdnimg.cn/20200812202245414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
那看一下具体的错误，
__RAM_segment_used_end__
_vectors
-------------没有定义
那就去tx_initialize_low_level.S 里面看一下，解释下很清楚了。（为什么全英文的东西愿意看，就是threadx里面文件注释太全了，虽然是英文但是至少比没有注释强太多了）

![](https://img-blog.csdnimg.cn/20200812202358800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812202634799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
 - 找到STM32某某_FLASH.ld，给他设置地址__RAM_segment_used_end__
 
```c
__RAM_segment_used_end__ = .;
```

![](https://img-blog.csdnimg.cn/20200812203450767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 找到 startup_stm32某某.s 里面 g_pfnVectors 中断向量 应该由 threadx 指向
替换_vector = g_pfnVectors ,再次编译
![](https://img-blog.csdnimg.cn/2020081220381551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200812204336372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
**nice ! no error !!! 接下来就是跑threadx的了**
![](https://img-blog.csdnimg.cn/20200812204447529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

## 验证

1. 首先加入头文件 "tx_api.h"
![](https://img-blog.csdnimg.cn/20200812204623655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
2. 调用 tx_kernel_enter()
![](https://img-blog.csdnimg.cn/20200812213130788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
3. 编写 void tx_application_define(void *first_unused_memory)就可以运行了

```c
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * <center>&copy; Copyright (c) 2020 STMicroelectronics.
  * All rights reserved.</center>
  *
  * This software component is licensed by ST under BSD 3-Clause license,
  * the "License"; You may not use this file except in compliance with the
  * License. You may obtain a copy of the License at:
  *                        opensource.org/licenses/BSD-3-Clause
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "spi.h"
#include "tim.h"
#include "usart.h"
#include "gpio.h"
#include "tx_api.h"
	........
	........
	........
/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */
TX_THREAD my_thread_1;
TX_THREAD my_thread_2;

uint8_t pData1[]="I am thread1 ";
uint8_t pData2[]="I am thread2 ";
/* USER CODE END PV */
	........
	........
	........
int main(void)
{
  /* USER CODE BEGIN 1 */
	uint8_t pData[]="=========ThreadX========= \n";
  /* USER CODE END 1 */
	........
	........
	........
  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_USART1_UART_Init();
  MX_TIM1_Init();
  MX_TIM2_Init();
  MX_TIM3_Init();
  MX_SPI3_Init();
  /* USER CODE BEGIN 2 */
  HAL_UART_Transmit(&huart1, pData, sizeof(pData), HAL_MAX_DELAY);

  tx_kernel_enter(); //threadx 入口

  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    /* USER CODE END WHILE */
    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}


/* USER CODE BEGIN 4 */
void thread1_entry(ULONG entry_input)
{
	while(1)
	{
		  HAL_UART_Transmit(&huart1, pData1, sizeof(pData1), HAL_MAX_DELAY);
		  tx_thread_sleep(1000);// 线程睡眠1000 timer_ticks
	}
}

void thread2_entry(ULONG entry_input)
{
	while(1)
	{
		  HAL_UART_Transmit(&huart1, pData2, sizeof(pData2), HAL_MAX_DELAY);
		  tx_thread_sleep(500);// 线程睡眠500 timer_ticks
	}
}

void tx_application_define(void *first_unused_memory)
{
	/*线程1*/
	tx_thread_create(
			&my_thread_1,	//线程控制块指针
			"my_thread1",	//线程名字
			thread1_entry,  //线程入口函数
			0,				//线程入口参数
			first_unused_memory, //线程的起始地址(这里偷懒,没有进行分配,直接使用未用的起始地址)
			1024,	//内存区域大小K
			3,		//优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,		//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START //线程自动启动
			);
	/*线程2*/
	tx_thread_create(
			&my_thread_2,	//线程控制块指针
			"my_thread2",	//线程名字
			thread2_entry,  //线程入口函数
			0,				//线程入口参数
			first_unused_memory+1024, //线程的起始地址+1024 (-被前面线程用掉了)
			1024,	//内存区域大小K
			1,		//优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,		//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START //线程自动启动
			);
}
/* USER CODE END 4 */
```
乍看以为ok了，但实际上还是有些小毛病的，时间好像对不上号啊，
我的时钟频率是80Mhz,延时的时间跟想法有出入。
这里还有个地方需要修改就是系统的OS tick，根据设置按需修改
![](https://img-blog.csdnimg.cn/20200812221607228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/2020081222463176.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
这样算出来应该差不了
![](https://img-blog.csdnimg.cn/20200812224712476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
完
