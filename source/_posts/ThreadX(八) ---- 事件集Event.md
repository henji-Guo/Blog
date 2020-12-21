---
title: ThreadX(八)---事件集Event
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(八)------事件集Event
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-08-28 22:04:56
---

## API 
- tx_event_flags_create
- tx_event_flags_delete
- tx_event_flags_get
- tx_event_flags_set
- tx_event_flags_set_notify

### tx_event_flags_create

```c
UINT tx_event_flags_create(TX_EVENT_FLAGS_GROUP *group_ptr,
    CHAR *name_ptr);
```

**参数**

- group_ptr指向事件标志组控件块的指针。
- name_ptr指向事件标志组的名称。

**返回值**

- TX_SUCCESS （0x00） 成功创建事件组。
- TX_GROUP_ERROR （0x06） 无效事件组指针。指针为 NULL 或已创建事件组。
- TX_CALLER_ERROR服务（0x13）无效调用。

### tx_event_flags_delete

```c
UINT tx_event_flags_delete(TX_EVENT_FLAGS_GROUP *group_ptr);
```

**参数**

- group_ptr指向以前创建的事件标志组。
 
**返回值**

- TX_SUCCESS （0x00） 成功事件标志组删除。
- TX_GROUP_ERROR （0x06） 无效事件标志组指针。
- TX_CALLER_ERROR服务（0x13）无效调用。


### tx_event_flags_get

```c
UINT tx_event_flags_get(TX_EVENT_FLAGS_GROUP *group_ptr,
    ULONG requested_flags, UINT get_option,
    ULONG *actual_flags_ptr, ULONG wait_option);
```

**参数**

- group_ptr 指向以前创建的事件标志组。

- requested_flags 32 位未签名的变量，表示请求的事件标志。

- get_option 指定是否需要所有或任何请求的事件标志。以下是有效的选择：
1. TX_AND （0x02）
2. TX_AND_CLEAR （0x03）
3. TX_OR （0x00）
4. TX_OR_CLEAR （0x01）

选择TX_AND或TX_AND_CLEAR指定组中必须存在所有事件标志。选择TX_OR或TX_OR_CLEAR指定任何事件标志令人满意。如果指定了请求或更改，则清除满足请求TX_AND_CLEAR（TX_OR_CLEAR为零）。

- actual_flags_ptr 指向检索到的事件标志放置位置的目标。请注意，获得的实际标志可能包含未请求的标志。
- wait_option定义未设置所选事件标志时服务的行为方式。等待选项的定义如下：
1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER超时值 （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到事件标志可用。
3. 超时值 （0x0000001 到 0xFFFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待事件标志时保持挂起计时器的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功事件标志。
- TX_DELETED时，删除了第 0x01 个事件标志组。
- TX_NO_EVENTS （0x07） 服务无法在指定时间内获取指定的事件等待。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_GROUP_ERROR （0x06） 无效事件标志组指针。
- TX_PTR_ERROR事件标志的"0x03"无效指针。
- TX_WAIT_ERROR （0x04） 在从非TX_NO_WAIT的呼叫上指定的等待选项（非线程） 以外的等待选项。
- TX_OPTION_ERROR （0x08） 无效获取选项。

### tx_event_flags_set


```c
UINT tx_event_flags_set(TX_EVENT_FLAGS_GROUP *group_ptr,
    ULONG flags_to_set,UINT set_option);
```

**参数**
- group_ptr 指向以前创建的事件标志组控件块的指针。

- flags_to_set 指定要根据所选的设置选项设置或清除的事件标志。
- set_option 指定的事件标志是 ANDed 还是 ORed 进入组的当前事件标志。
以下是有效的选择：
1. TX_AND （0x02）
2. TX_OR （0x00）
选择TX_AND指定指定的事件标志和ed 到组中的当前事件标志。此选项通常用于清除组中的事件标志。否则，如果TX_OR，指定的事件标志将用组中的当前事件进行 OR ed。

**返回值**

- TX_SUCCESS （0x00） 成功事件标志集。
- TX_GROUP_ERROR （0x06） 到事件标志组的无效指针。
- TX_OPTION_ERROR （0x08） 指定的无效设置选项。

### tx_event_flags_set_notify


```c
UINT tx_event_flags_set_notify(TX_EVENT_FLAGS_GROUP *group_ptr,
    VOID (*events_set_notify)(TX_EVENT_FLAGS_GROUP *));
```

**参数**

- group_ptr 指向以前创建的事件标志组。
- events_set_notify 指向应用程序的事件标志设置通知函数。如果此值为TX_NULL，则禁用通知。

**返回值**

- TX_SUCCESS （0x00） 成功注册事件标志集通知。
- TX_GROUP_ERROR （0x06） 无效事件标志组指针。
- TX_FEATURE_NOT_ENABLED （0xFF） 系统已编译，并禁用了通知功能。

## Event _Flags_demo

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * Event _Flags_demo.c
 * Change Logs:
 * Date           Author       Notes
 * 2020年8月25日     	  henji      the first version
 */
/* USER CODE BEGIN Includes */
#include "tx_api.h"
#include "stdio.h"

/* USER CODE BEGIN PV */
TX_THREAD MyThread_1;
TX_THREAD MyThread_2;

/*线程栈大小*/
#define DEMO_STACK_SIZE 1024
/*内存池总大小*/
#define DEMO_BYTE_POOL_SIZE 1024*5
/*内存块池总大小*/
#define DEMO_BLOCK_POOL_SIZE 100
/*内存字节池控制块*/
TX_BYTE_POOL byte_pool_0;

/* 指向内存的指针 */
UCHAR *memory_ptr;

/* 事件组 */
TX_EVENT_FLAGS_GROUP event_flags_0;

UINT count_A = 0;
UINT count_B = 0;
CHAR buffer[64];
/* Tracex使用 */
/*跟踪缓冲区的内存大小*/
#define trace_buffer_size 64000
/*要保留在跟踪注册表中的应用程序ThreadX对象的数量*/
#define registry_entries 40
UCHAR trace_buffer_start[trace_buffer_size];
UINT trace_status;
/* USER CODE END PV */
	......
int main(void)
{
	........
	/* USER CODE BEGIN 2 */

	tx_kernel_enter(); //threadx 入口

	/* USER CODE END 2 */

	/* Infinite loop */
	/* USER CODE BEGIN WHILE */
	while (1);
}

void MyThread_1_entry(ULONG entry_input)
{
	ULONG status;
	ULONG actual_flags;
	while (1)
	{

		status = tx_event_flags_get(&event_flags_0, //事件控制块
						   	   	   0x00000111,	   //事件1、4、8标志
								   TX_AND_CLEAR,   //事件1、4、8同时置位触发,且清零
								   &actual_flags,  //保存复位前的状态
								   5 //超时2 ticks
									);
		if(status == TX_SUCCESS)
		{
			HAL_UART_Transmit(&huart1, (uint8_t *)"Event flag: 1 & 4 & 8 ",sizeof("Event flag: 1 & 4 & 8 "),HAL_MAX_DELAY);
			tx_event_flags_set(&event_flags_0, //事件组控制块
							   0x00000001, 	   //设置事件1标志位
					           TX_OR           //TX_OR 当前置位
							  );
		}


		status = tx_event_flags_get(&event_flags_0, //事件控制块
						   	   	   0x00000001,	   //事件1标志
								   TX_AND_CLEAR,   //事件1置位触发,且不清零
								   &actual_flags,  //保存复位前的状态
								   5 //超时20 ticks
									);
		if(status == TX_SUCCESS)
		{
			HAL_UART_Transmit(&huart1, (uint8_t *)"Event flag: 1 ",sizeof("Event flag: 1 "),HAL_MAX_DELAY);
			tx_event_flags_set(&event_flags_0, //事件组控制块
							   0x00000011, 	   //设置事件1、4标志位
					           TX_OR           //TX_OR 当前置位
							  );
		}

		tx_thread_sleep(5);
	}
}

void MyThread_2_entry(ULONG entry_input)
{
	    ULONG status;
		ULONG actual_flags;
		while (1)
		{

			status = tx_event_flags_get(&event_flags_0, //事件控制块
							   	   	   0x00000011,	   //事件1标志
									   TX_AND_CLEAR,   //事件1置位触发,且清零
									   &actual_flags,  //保存复位前的状态
									   5 //超时2 ticks
										);
			if(status == TX_SUCCESS)
			{
				HAL_UART_Transmit(&huart1, (uint8_t *)"Event flag: 1 & 4 ",sizeof("Event flag: 1 & 4 "),HAL_MAX_DELAY);
				tx_event_flags_set(&event_flags_0, //事件组控制块
								   0x00000111, 	   //设置事件8标志位
						           TX_OR           //TX_OR 当前置位
								  );
			}
			tx_thread_sleep(5);

		}

}

void tx_application_define(void *first_unused_memory)
{
	/*使能追踪*/
	trace_status = tx_trace_enable(&trace_buffer_start, trace_buffer_size,registry_entries);

	/*创建一个内存池用于分配线程栈*/
	tx_byte_pool_create(&byte_pool_0, 		//内存池的指针
			"byte pool 0",//名称
			first_unused_memory,//分配内存地址
			DEMO_BYTE_POOL_SIZE//分配内存池大小
			);

	/*分配一个栈空间用于线程1*/
	tx_byte_allocate(&byte_pool_0,		   //内存池的指针
			(VOID**) &memory_ptr,		   //指向目标内存指针的指针
			DEMO_STACK_SIZE,     //分配栈大小
			TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
			);
	/*线程1*/
	tx_thread_create(&MyThread_1,	//线程控制块指针
			"MyThread_1",//线程名字
			MyThread_1_entry,//线程入口函数
			0,//线程入口参数
			memory_ptr,//线程的起始地址
			DEMO_STACK_SIZE,//线程栈大小 K
			1,//优先级1 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);
	/*分配一个栈空间用于线程2*/
	tx_byte_allocate(&byte_pool_0,		   //内存池的指针
			(VOID**) &memory_ptr,		   //指向目标内存指针的指针
			DEMO_STACK_SIZE,     //分配栈大小
			TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
			);
	/*线程2*/
	tx_thread_create(&MyThread_2,	//线程控制块指针
			"MyThread_2",//线程名字
			MyThread_2_entry,//线程入口函数
			0,//线程入口参数
			memory_ptr,//线程的起始地址
			DEMO_STACK_SIZE,//线程栈大小 K
			3,//优先级3 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);

	/* 创建事件组 */
	tx_event_flags_create(&event_flags_0,"event flags 0");

	/* 一个事件组包含32个事件,0 ~ 31 */
	/* 一位十六进制 表示 四位二进制 ,也就是说一位十六进制 涵盖了 4 个 事件*/
	/* 0x00000001   表示事件1*/
	/* 0x00000011   表示事件1和4*/
	/* 0x????????*/

	tx_event_flags_set(&event_flags_0, //事件组控制块
					   0x00000111, 	   //设置事件1、4、8标志位
			           TX_OR           //TX_OR 当前置位 、 TX_AND 当前复位 相当于取反事件(除去0、4、8)
					  );


}

```


![](https://img-blog.csdnimg.cn/20200827224806729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

![](https://img-blog.csdnimg.cn/20200827224615515.png#pic_center)
完