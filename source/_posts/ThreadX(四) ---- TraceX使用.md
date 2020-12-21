---
title: ThreadX(四)------TraceX使用
author: henji
img: 
top: false
# cover: true
coverImg: 
password: 
toc: true
mathjax: false
summary: ThreadX(四)------TraceX使用
categories: ThreadX
tags:
  - ThreadX
date: 2020-08-22 10:06:46
---
## 简介
Azure RTOS TraceX是一个Microsoft系统分析工具，它显示由运行在嵌入式目标上的ThreadX收集的系统事件信息。用户负责将存储在嵌入式目标中RAM中的跟踪缓冲区转移到主机上的二进制文件中。然后，用户可以使用TraceX打开此文件，并以图形方式分析目标事件，诊断系统问题并调整工作的应用程序以提高性能和资源管理。

## TraceX 软件
[点击下载：TraceX](http://www.armbbs.cn/forum.php?mod=attachment&aid=NjA0NzV8M2YzZDY3MTh8MTU5NzkyNzgzNHwzMDQ3M3w5NzkyNQ%3D%3D)

![](https://img-blog.csdnimg.cn/20200821225656893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

## Trace API
- tx_trace_enable：启用事件跟踪
- tx_trace_event_filter：过滤指定的事件
- tx_trace_event_unfilter：取消过滤指定的事件
- tx_trace_disable：禁用事件跟踪
- tx_trace_isr_enter_insert：插入ISR输入跟踪事件
- tx_trace_isr_exit_insert：插入ISR退出跟踪事件
- tx_trace_buffer_full_notify：注册跟踪缓冲区已满的应用程序回调
- tx_trace_user_event_insert：插入用户事件

### tx_trace_enable

```c
UINT tx_trace_enable (VOID *trace_buffer_start,
     ULONG trace_buffer_size, ULONG registry_entries);
```
**输入参数**
- trace_buffer_start：指向用户提供的跟踪缓冲区的开始的指针。
- trace_buffer_size：跟踪缓冲区的内存中的字节总数。跟踪缓冲区越大，它可以存储的条目越多。
- Registry_entries：要保留在跟踪注册表中的应用程序ThreadX对象的数量。注册表用于将对象地址与对象名称相关联。这对于GUI跟踪分析工具非常有用。

**返回值**
- TX_SUCCESS（0x00）成功的事件跟踪启用。
- TX_SIZE_ERROR（0x05）指定的跟踪缓冲区大小太小。它必须足够大以容纳跟踪头，对象注册表和至少一个跟踪条目。
- TX_NOT_DONE（0x20）事件跟踪已启用。
- TX_FEATURE_NOT_ENABLED（0xFF）系统未在启用跟踪的情况下进行编译。

### tx_trace_buffer_full_notify

```c
VOID tx_trace_buffer_full_notify (VOID (*full_buffer_callback)(VOID *));
```
**输入参数**

- full_buffer_callback：跟踪缓冲区已满时调用的应用程序函数。NULL值将禁用通知回调。

**example**

```c
trace_is_full(void *trace_buffer_start)
{
     /*停止跟踪 or 另辟buffer*/
}
/* 注册回调函数 */
tx_trace_buffer_full_notify (trace_is_full);
```
[具体API详情： 点击访问](https://docs.microsoft.com/en-us/azure/rtos/tracex/chapter5#exporting-the-trace-buffer)


## 生成跟踪buf

 1. 启用宏定义 TX_ENABLE_EVENT_TRACE (一般在tx_user.h内开启，具体的内容在tx_port.h & tx_trace.h）
这里直接在tx_port.h里面定义了，源码下tx_user_sample.h(修改为tx_user.h 里面根据需要开启)

![](https://img-blog.csdnimg.cn/20200821230851759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

![](https://img-blog.csdnimg.cn/20200821230923556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

1. 定义时间戳常量(这里使用的是默认的没改...)

![](https://img-blog.csdnimg.cn/20200821231320320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

1. 调用 tx_trace_enable( )
在main.c 中定义好相关参数，然后只要把 tx_trace_enable( ) 放到任意一个线程任务while(1)循环里面，就是实现跟踪了，(这里trace单独一个线程了，以示区分)代码大致入下如下图所示：

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * main.c
 * Change Logs:
 * Date		Author		Notes
 * 2020年8月21日	henji		the first version
 */
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
/* USER CODE END Includes */
 ........
/* USER CODE BEGIN PV */
TX_THREAD my_thread_1;
TX_THREAD my_thread_2;
TX_THREAD trace_thread;
uint8_t pData[] = "=========ThreadX=========\n";
uint8_t pData1[] = "I am thread1 ";
uint8_t pData2[] = "I am thread2 ";

/* Tracex使用 */
/*跟踪缓冲区的内存大小*/
#define trace_buffer_size 64000
/*要保留在跟踪注册表中的应用程序ThreadX对象的数量*/
#define registry_entries 40
UCHAR trace_buffer_start[trace_buffer_size];
UINT trace_status;
/* USER CODE END PV */

int main(void)
{
    .......
	/* USER CODE BEGIN 2 */
	tx_kernel_enter(); //threadx 入口
	/* USER CODE END 2 */
	while (1);
}

/* USER CODE BEGIN 4 */
void thread1_entry(ULONG entry_input)
{
	INT count = 0;
	uint8_t init_data[] = "start now";
	while (1)
	{

		HAL_UART_Transmit(&huart1, pData1, sizeof(pData1), HAL_MAX_DELAY);
		if (count == 0)
		{
			HAL_UART_Transmit(&huart1, init_data, sizeof(init_data),
			HAL_MAX_DELAY);
		}
		count++;
		HAL_GPIO_TogglePin(GPIOE, GPIO_PIN_7 | GPIO_PIN_8);
		tx_thread_sleep(100);
	}
}

void thread2_entry(ULONG entry_input)
{
	INT count = 0;
	while (1)
	{
		HAL_UART_Transmit(&huart1, pData2, sizeof(pData2), HAL_MAX_DELAY);
		if (count == 3)
		{
			/*挂起线程1*/
			tx_thread_suspend(&my_thread_1);
		}
		else if (count == 6)
		{
			/*恢复线程1*/
			tx_thread_resume(&my_thread_1);
		}
		else if (count == 9)
		{
			/*终止线程1*/
			tx_thread_terminate(&my_thread_1);
		}
		else if (count == 12)
		{
			/*重置线程1*/
			tx_thread_reset(&my_thread_1);
			/*恢复线程1*/
			tx_thread_resume(&my_thread_1);
		}
		else if (count == 13)
		{
			/*终止线程1*/
			//tx_thread_terminate(&my_thread_1);
			//tx_thread_terminate(&my_thread_2);
		}
		else
		{
			;
		}
		count++;

		tx_thread_sleep(200);
	}
}

void trace_thread_input(ULONG entry_input)
{
	while (1)
	{
		/*使能追踪*/
		trace_status = tx_trace_enable(&trace_buffer_start, trace_buffer_size, registry_entries);
		if (trace_status == TX_SUCCESS)
		{
			; //使能成功
		}
		if (trace_status == TX_NOT_DONE)
		{
			; //在追踪
		}
		tx_thread_sleep(100);
	}
}

void my_entry_exit_notify(TX_THREAD *thread_ptr, UINT condition)
{
	uint8_t entry_data[] = " thread1-entry ";
	uint8_t exit_data[] = " thread1-exit ";
	/* Determine if the thread was entered or exited. */

	if (condition == TX_THREAD_ENTRY)
	{
		/* Thread entry! */
		HAL_UART_Transmit(&huart1, entry_data, sizeof(pData2), HAL_MAX_DELAY);
	}
	if (condition == TX_THREAD_EXIT)
	{
		/* Thread exit! */
		HAL_UART_Transmit(&huart1, exit_data, sizeof(pData2), HAL_MAX_DELAY);
	}

}

void tx_application_define(void *first_unused_memory)
{
	/*线程1*/
	tx_thread_create(&my_thread_1,	//线程控制块指针
			"my_thread1",//线程名字
			thread1_entry,//线程入口函数
			0,//线程入口参数
			first_unused_memory,//线程的起始地址(这里偷懒,没有进行分配,直接使用未用的起始地址)
			1024,//内存区域大小K
			3,//优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);
	/*线程2*/
	tx_thread_create(&my_thread_2,	//线程控制块指针
			"my_thread2",//线程名字
			thread2_entry,//线程入口函数
			0,//线程入口参数
			first_unused_memory+1024,//线程的起始地址+1024 (-被前面线程用掉了)
			1024,//内存区域大小K
			2,//优先级2 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			2,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);

	/*trace 线程*/
	tx_thread_create(&trace_thread,	//线程控制块指针
			"trace_thread",//线程名字
			trace_thread_input,//线程入口函数
			0,//线程入口参数
			first_unused_memory+2048, 1024,	//内存区域大小K
			1,//优先级2 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);
	/*线程进入和退出时通知*/
	tx_thread_entry_exit_notify(&my_thread_1, my_entry_exit_notify);

}
/* USER CODE END 4 */
```

### 导出trx 跟踪文件
 1. 点击debug 进入调试状态 ，运行一会然后暂停，找到trace buffer 内存区，将数据导出。(IDE 有很多 mdk IAR ES ..... 这里还是选择ST公司的STM32CubeIDE , 不为什么，单纯 免费。ES 也免费，毕竟segger公司的，调试仅J-link ,STM32CubeIDE  可以支持OpenOCD 基本上满足 st-link 、 j-link 、DAP 等调试器了 )


![](https://img-blog.csdnimg.cn/20200822085215894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20200822085455440.png#pic_center)

Memory  && Memory Browser 都可以

![](https://img-blog.csdnimg.cn/20200822085537871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

首地址只要把鼠标放在上面就可以找到了

![](https://img-blog.csdnimg.cn/20200822085849369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

点击导出 export

![](https://img-blog.csdnimg.cn/20200822090036142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

![](https://img-blog.csdnimg.cn/20200822090353417.png#pic_center)

### 使用TraceX 导入

![](https://img-blog.csdnimg.cn/20200822090706308.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

关于tracex更具体使用，就参考[官方文档](https://docs.microsoft.com/en-us/azure/rtos/tracex/about-this-guide)吧

![](https://img-blog.csdnimg.cn/20200822090928796.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

完

