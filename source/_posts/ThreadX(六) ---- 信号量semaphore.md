---
title: ThreadX(六) ------ 信号量semaphore
author: henji
img: 
top: false
# cover: true
coverImg: 
password: 
toc: true
mathjax: false
summary: ThreadX(六) ------ 信号量semaphore
categories: ThreadX
tags:
  - ThreadX
date: 2020-08-26 20:06:46
---

## 概述 
ThreadX提供32位计数信号量，范围在0到4,294,967,295之间。 有两种用于计数信号量的操作：tx_semaphore_get和tx_semaphore_put。 get操作将信号量减一。 如果信号量为0，则get操作不会成功。 与get操作相反的是put操作。 它使信号量增加一。
每个计数信号量都是公共资源。 ThreadX对如何使用计数信号量没有任何限制。


## API
- tx_semaphore_create
- tx_semaphore_delete
- tx_semaphore_get
- tx_semaphore_put
- tx_semaphore_put_notify


### tx_semaphore_create

```c
UINT tx_semaphore_create(TX_SEMAPHORE *semaphore_ptr,
    CHAR *name_ptr, ULONG initial_count);
```

**参数**

- semaphore_ptr指向信号量控件块的指针。
- name_ptr指向信号量名称的指针。
- initial_count指定此信号量的初始计数。法律值的范围为 0x000000000 到 0xFFFF。

**返回值**

- TX_SUCCESS （0x00） 成功创建信号量。
- TX_SEMAPHORE_ERROR （0x0C） 无效信号量指针。指针为 NULL 或已创建信号量。
- TX_CALLER_ERROR服务（0x13）无效调用。


### tx_semaphore_delete

```c
UINT tx_semaphore_delete(TX_SEMAPHORE *semaphore_ptr);
```

**参数**

- semaphore_ptr指向以前创建信号量的指针。

**返回值**

 
- TX_SUCCESS （0x00） 成功计数信号量删除。
- TX_SEMAPHORE_ERROR （0x0C） 无效计数信号量指针。
- TX_CALLER_ERROR服务（0x13）无效调用。


### tx_semaphore_get

```c
UINT tx_semaphore_get(TX_SEMAPHORE *semaphore_ptr,
    ULONG wait_option);
```

**参数**

- semaphore_ptr    指向以前创建的计数信号量。
-  wait_option 定义服务的行为方式（如果没有可用的信号量实例）;
即，信号量计数为零。等待选项的定义如下：
1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到信号量实例可用。
超时值 （0x0000001 到 0xFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待信号量实例时保持挂起计时器的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功检索信号量实例。
- TX_DELETED （0x01） 计数信号量在线程挂起时被删除。
- TX_NO_INSTANCE （0x0D） 服务无法检索计数信号量的实例（信号量计数在指定等待时间内为零）。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_SEMAPHORE_ERROR （0x0C） 无效计数信号量指针。
- TX_WAIT_ERROR （0x04） 在非线程的TX_NO_WAIT上指定了除其他帐户以外的等待选项。


### tx_semaphore_put

```c
UINT tx_semaphore_put(TX_SEMAPHORE *semaphore_ptr);
```

**参数**

- semaphore_ptr指向以前创建的计数信号量控件块的指针。

**返回值**

- TX_SUCCESS （0x00） 成功放入信号量。
- TX_SEMAPHORE_ERROR （0x0C） 到计数信号量的无效指针。

### tx_semaphore_put_notify

```c
UINT tx_semaphore_put_notify(TX_SEMAPHORE *semaphore_ptr,
    VOID (*semaphore_put_notify)(TX_SEMAPHORE *));
```
**参数**

- semaphore_ptr指向以前创建信号量的指针。
- semaphore_put_notify指向应用程序信号量 put 通知函数的指针。如果此值为TX_NULL，则禁用通知。
- 
**返回值**

- TX_SUCCESS （0x00） 成功注册信号量放通知。
- TX_SEMAPHORE_ERROR （0x0C） 无效信号量指针。
- TX_FEATURE_NOT_ENABLED （0xFF） 系统已编译，并禁用了通知功能。

## semaphore_demo

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * semaphore_demo.c
 * Change Logs:
 * Date		Author		Notes
 * 2020年8月22日	henji		the first version
 */
	........
	
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
/* USER CODE END Includes */

/* USER CODE BEGIN PV */
TX_THREAD Producer;
TX_THREAD Consumer;

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

/*信号量  空的篮子*/
TX_SEMAPHORE sem_empty;

/*信号量  临界区*/
TX_SEMAPHORE sem_lock;

/*信号量  满的篮子*/
TX_SEMAPHORE sem_full;

/*信号量状态*/
UINT status;

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

	/* Infinite loop */
	/* USER CODE BEGIN WHILE */
	while (1);
}


/* USER CODE BEGIN 4 */
void producer_entry(ULONG entry_input)
{

	while (1)
	{
		/*获取空篮子信号量*/
		status = tx_semaphore_get(&sem_empty, TX_WAIT_FOREVER);
		if (status == TX_SUCCESS)
		{
			/*获取空篮子成功*/;
		}

		/*临界区上锁*/
		tx_semaphore_get(&sem_lock, TX_WAIT_FOREVER);

		/*临界区   生产者生产*/
		HAL_UART_Transmit(&huart1, (uint8_t*) "Producing...",sizeof("Producing..."), HAL_MAX_DELAY);
		/*临界区释放锁*/
		tx_semaphore_put(&sem_lock);

		/*释放满篮子信号量*/
		tx_semaphore_put(&sem_full);

		tx_thread_sleep(20);
	}
}

void consumer_entry(ULONG entry_input)
{
	while (1)
	{
		/*获取满篮子信号量*/
		status = tx_semaphore_get(&sem_full, TX_WAIT_FOREVER);
		if (status == TX_SUCCESS)
		{
			/*获取满篮子成功*/;
		}

		/*临界区上锁*/
		tx_semaphore_get(&sem_lock, TX_WAIT_FOREVER);

		/*临界区 消费者消费*/
		HAL_UART_Transmit(&huart1, (uint8_t*) "Consuming...",sizeof("Consuming..."), HAL_MAX_DELAY);
		/*临界区释放锁*/
		tx_semaphore_put(&sem_lock);

		/*释放空篮子信号量*/
		tx_semaphore_put(&sem_empty);

		tx_thread_sleep(10);
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

	/*创建线程1*/
	tx_thread_create(&Producer,	//线程控制块指针
			"my_thread1",//线程名字
			producer_entry,//线程入口函数
			0,//线程入口参数
			memory_ptr,//线程的起始地址
			DEMO_STACK_SIZE,//线程栈大小 K
			2,//优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			2,//禁用抢占的最高优先级
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
	tx_thread_create(&Consumer,	//线程控制块指针
			"my_thread2",//线程名字
			consumer_entry,//线程入口函数
			0,//线程入口参数
			memory_ptr,//线程的起始地址
			DEMO_STACK_SIZE,//线程栈大小 K
			3,//优先级2 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);

	/* 篮子数量一个 */
	/*创建信号量 空的篮子 */
	tx_semaphore_create(&sem_empty, "sem_empty", 1);

	/*创建信号量 满的篮子 */
	tx_semaphore_create(&sem_full, "sem_full", 0);

	/*创建信号量 临界区 */
	tx_semaphore_create(&sem_lock, "sem_lock", 1);

}
/* USER CODE END 4 */
```
![](https://img-blog.csdnimg.cn/20200823142222128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)
完

