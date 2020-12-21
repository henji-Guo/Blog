---
title: ThreadX(二) ------ 移植STM32[Keil] # 文章标题  
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(二) ------ 移植STM32[Keil]
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-08-30 10:04:56
---

## 裸机项目（Keil）
确保裸机项目没问题
![](https://img-blog.csdnimg.cn/20200829095850105.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200829095939757.png#pic_center)


## 移植ThreadX
- 添加头文件路径
![](https://img-blog.csdnimg.cn/20200829103353583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

- 添加源码路径
![](https://img-blog.csdnimg.cn/20200829103536216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 修改编译（用armc6）
![](https://img-blog.csdnimg.cn/20200829103901869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
armc5 的话是没有这个的，会报一推错误
![](https://img-blog.csdnimg.cn/20200829104012302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
- 第一次编译

重复定义错误，注释掉 xxxx.it.c 里面对应的函数
![](https://img-blog.csdnimg.cn/20200829104156750.png#pic_center)
![](https://img-blog.csdnimg.cn/20200829104405247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

- 第二次编译

七个错误一个一个来
![](https://img-blog.csdnimg.cn/20200829104452486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
1.  __RAM_segment_used_end__ = __initial_sp  初始化起始地址
![](https://img-blog.csdnimg.cn/20200829104710420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
2. 把 _vectors 全部替换成 startup.......s 里面的  __Vectors
![](https://img-blog.csdnimg.cn/20200829105100595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200829105151270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
3. 注释 @
	- _tx_execution_isr_exit   
	- _tx_execution_isr_entry
	- _tx_execution_thread_enter
	- _tx_execution_thread_exit
![](https://img-blog.csdnimg.cn/20200829105316270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200829105428761.png#pic_center)
![](https://img-blog.csdnimg.cn/20200829105509859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

- 第三次编译

在main.c里面添加 "tx_api.h"
main()函数while(1)前添加 	tx_kernel_enter( );

实现函数 void tx_application_define(void *first_unused_memory)

```c
	.......
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
/* USER CODE END Includes */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */
TX_THREAD my_thread1;

TX_THREAD my_thread2;
/* USER CODE END PV */

	.......
int main(void)
{
	........
	
  /* USER CODE BEGIN 2 */
	HAL_UART_Transmit(&huart1,(uint8_t *)"====ThreadX====",sizeof("====ThreadX===="),HAL_MAX_DELAY);
  
	tx_kernel_enter( );
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


void my_thread1_entry(ULONG thread_input)
{
  /* Enter into a forever loop. */
  while(1)
  {
   
		HAL_UART_Transmit(&huart1,(uint8_t *)"threadx 1 ...",sizeof("threadx 1 ..."),HAL_MAX_DELAY);
   
    tx_thread_sleep(100);
  }
}
void my_thread2_entry(ULONG thread_input)
{
  /* Enter into a forever loop. */
  while(1)
  {
    
		HAL_UART_Transmit(&huart1,(uint8_t *)"threadx 2 ...",sizeof("threadx 2 ..."),HAL_MAX_DELAY);
    
    tx_thread_sleep(300);
  }
}

void tx_application_define(void *first_unused_memory)
{
  /* Create my_thread! */
  tx_thread_create(&my_thread1, "My Thread 1",
  my_thread1_entry, 0, first_unused_memory, 1024, 3, 3, TX_NO_TIME_SLICE, TX_AUTO_START);
  
  tx_thread_create(&my_thread2, "My Thread 2",
  my_thread2_entry, 0, first_unused_memory+1024, 1024, 1, 1, TX_NO_TIME_SLICE, TX_AUTO_START);
}


```

![](https://img-blog.csdnimg.cn/20200829110804576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

