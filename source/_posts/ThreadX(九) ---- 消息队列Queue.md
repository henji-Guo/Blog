---
title: ThreadX(九)---消息队列Queue
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(九)------消息队列Queue
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-08-28 22:06:22
---

## API 
- tx_queue_create
- tx_queue_delete
-  tx_queue_flush
- tx_queue_front_send
- 	tx_queue_receive
- 	tx_queue_send_notify

### tx_queue_create

```c
UINT tx_queue_create(TX_QUEUE *queue_ptr, CHAR *name_ptr,
    UINT message_size,
    VOID *queue_start, ULONG queue_size);
```

**参数**

- queue_ptr 指向消息队列控件块的指针。
- name_ptr 指向消息队列的名称的指针。
- message_size 指定队列中每条消息的大小。消息大小范围从 1 个 32 位单词到 16 个 32 位单词。有效消息大小选项是 1 到 16 的数值（含）。
- queue_start 消息队列的起始地址。起始地址必须与 ULONG 数据类型的大小对齐。
- queue_size 消息队列可用的字节总数。

**返回值**

- TX_SUCCESS （0x00） 成功创建消息队列。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。指针为 NULL 或队列已创建。
- TX_PTR_ERROR （0x03） 消息队列的无效起始地址。
- TX_SIZE_ERROR （0x05） 消息队列的大小无效。
- TX_CALLER_ERROR服务（0x13）无效调用。

### tx_queue_delete

```c
UINT tx_queue_delete(TX_QUEUE *queue_ptr);
```

**参数**

- queue_ptr 指向以前创建的消息队列的指针。

**返回值**

- TX_SUCCESS （0x00） 成功删除消息队列。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。
- TX_CALLER_ERROR服务（0x13）无效调用。

### x_queue_flush

```c
UINT tx_queue_flush(TX_QUEUE *queue_ptr);
```

**参数**

- queue_ptr 指向以前创建的消息队列的指针。

**返回值**

- TX_SUCCESS （0x00） 成功消息队列刷新。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。

### tx_queue_front_send

```c
UINT tx_queue_front_send(TX_QUEUE *queue_ptr,
    VOID *source_ptr, ULONG wait_option);
```
**参数**
- queue_ptr 指向消息队列控件块的指针。
- source_ptr 指向消息的指针。
- wait_option 定义如果消息队列已满，服务的行为方式。等待选项的定义如下：

1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到队列中存在空间。
3. 超时值 （0x0000001 到 0xFFFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待队列中的房间时保持挂起的计时器刻度的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功发送消息。
- TX_DELETED时，已删除消息队列 （0x01） 消息队列。
- TX_QUEUE_FULL （0x0B） 服务无法发送消息，因为队列在指定等待的持续时间内已满。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。
- TX_PTR_ERROR （0x03） 消息的无效源指针。
- TX_WAIT_ERROR （0x04） 在非线程的TX_NO_WAIT上指定了除其他帐户以外的等待选项。

### tx_queue_receive

```c
UINT tx_queue_receive(TX_QUEUE *queue_ptr,
    VOID *destination_ptr, ULONG wait_option);
```

参数

- queue_ptr 指向以前创建的消息队列。
- destination_ptr 复制邮件的位置。
- wait_option 定义如果消息队列为空，服务的行为方式。等待选项的定义如下：

1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER （0xFFFF） - TX_WAIT_FOREVER调用线程将无限期挂起，直到消息可用。
3. 超时值 （0x0000001 到 0xFFFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待消息时保持挂起计时器刻度的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功检索消息。
- TX_DELETED时，已删除消息队列 （0x01） 消息队列。
- TX_QUEUE_EMPTY （0x0A） 服务无法检索消息，因为队列在指定等待的持续时间内为空。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。
- TX_PTR_ERROR （0x03） 消息的无效目标指针。
- TX_WAIT_ERROR （0x04） 在从非TX_NO_WAIT的呼叫上指定的等待选项（非线程） 以外的等待选项。

### tx_queue_send

```c
UINT tx_queue_send(TX_QUEUE *queue_ptr,
    VOID *source_ptr, ULONG wait_option);
```

**参数**
- queue_ptr 指向以前创建的消息队列。
- source_ptr 指向消息的指针。
- wait_option 定义如果消息队列已满，服务的行为方式。等待选项的定义如下：
1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到队列中存在空间。
3. 超时值 （0x0000001 到 0xFFFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待队列中的房间时保持挂起的计时器刻度的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功发送消息。
- TX_DELETED时，已删除消息队列 （0x01） 消息队列。
- TX_QUEUE_FULL （0x0B） 服务无法发送消息，因为队列在指定等待的持续时间内已满。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_QUEUE_ERROR （0x09） 无效消息队列指针。
- TX_PTR_ERROR （0x03） 消息的无效源指针。
- TX_WAIT_ERROR （0x04） 在从非TX_NO_WAIT的呼叫上指定的等待选项（非线程） 以外的等待选项。



### tx_queue_send_notify

```c
UINT tx_queue_send_notify(TX_QUEUE *queue_ptr,
    VOID (*queue_send_notify)(TX_QUEUE *));
```

**参数**

- queue_ptr	指向以前创建的队列的指针。
- queue_send_notify	指向应用程序的队列发送通知函数的指针。如果此值为TX_NULL，则禁用通知。

**返回值**

- TX_SUCCESS （0x00） 成功注册队列发送通知。
- TX_QUEUE_ERROR （0x09） 无效队列指针。
- TX_FEATURE_NOT_ENABLED （0xFF） 系统已编译，并禁用了通知功能。

## Queue_demo

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * Queue_demo.c
 * Change Logs:
 * Date           Author       Notes
 * 2020年8月28日     	  henji      the first version
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

/* 消息队列 */
TX_QUEUE my_queue;

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
	INT thread_1_send = 'A';
	INT special_send = 'Z';
	CHAR buf_send[19];
	while (1)
	{

		/* 紧急消息 */
		if(thread_1_send == 'C')
		{
			status = tx_queue_front_send(&my_queue,      //消息队列控制块
								         &special_send, //发送消息内容指针
									     TX_WAIT_FOREVER //无限等待
									     );
					if(status == TX_SUCCESS)
					{
						sprintf(buf_send,"special_send %c ",special_send);
						HAL_UART_Transmit(&huart1, (uint8_t*)buf_send, sizeof(buf_send), HAL_MAX_DELAY);
					}

		}
		else if(thread_1_send == 'I')
		{
			/* 到 'I' 的时候就结束进程  */
			tx_thread_terminate(&MyThread_1);
		}
		else
		{
			status = tx_queue_send(&my_queue,      //消息队列控制块
								   &thread_1_send, //发送消息内容指针
								   TX_WAIT_FOREVER //无限等待
								   );
					if(status == TX_SUCCESS)
					{
						sprintf(buf_send,"thread_1_send %c ",thread_1_send);
						HAL_UART_Transmit(&huart1, (uint8_t*)buf_send, sizeof(buf_send), HAL_MAX_DELAY);
					}
		}

		thread_1_send++;

		tx_thread_sleep(500);
	}
}

void MyThread_2_entry(ULONG entry_input)
{
	    ULONG status;
		INT thread_2_received;
		CHAR buf_rec[21];
		while (1)
		{
			status = tx_queue_receive(&my_queue, //消息队列控制块
								      &thread_2_received,//发送消息内容指针
					                  TX_WAIT_FOREVER //无限等待
					              );
			if(status == TX_SUCCESS)
			{
				sprintf(buf_rec,"thread_2_received %c ",thread_2_received);
				HAL_UART_Transmit(&huart1, (uint8_t*)buf_rec, sizeof(buf_rec), HAL_MAX_DELAY);
			}
			tx_thread_sleep(2000);

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

	/*分配一个空间用于消息队列*/
		tx_byte_allocate(&byte_pool_0,//内存池的指针
				(VOID**) &memory_ptr, //指向目标内存指针的指针
				100,   //分配消息队列大小 100字节
				TX_NO_WAIT //无论它是否成功，都会立即从该服务返回
				);
		/* 创建消息队列 */
		tx_queue_create(&my_queue, //消息队列控制块
				"my_queue",		   //消息队列名称
				4,				   //每一个消息大小
				memory_ptr,		   //消息队列地址
				100				   //总大小100
				);


}
/* USER CODE END 4 */

```


thread_1 发送 'A' ---> 'H'   【500 ticks】

thread_1 发送 'C' 的时候转变位紧急消息 

thread_1 发送 'I' 的时候终止进程    

thread_2 接受     【2000 ticks】

![](https://img-blog.csdnimg.cn/20200828203932261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

![](https://img-blog.csdnimg.cn/20200828204913469.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

![](https://img-blog.csdnimg.cn/2020082820493934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

完