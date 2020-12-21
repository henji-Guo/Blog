---
title: ThreadX(七)---互斥量Mutex
author: henji
img: #/source/images/xxx.jpg
top: false
cover: false
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(七) ------ 互斥量Mutex
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-08-28 22:02:29
---

## 概述
除了信号量外，ThreadX还提供了一个互斥对象。 互斥锁基本上是二进制信号量，这意味着一次只有一个线程可以拥有一个互斥锁。 此外，同一线程可以多次对拥有的互斥锁执行一次成功的互斥锁获取操作，确切地说是4,294,967,295。 互斥对象有两个操作：tx_mutex_get和tx_mutex_put。 get操作获取不属于另一个线程的互斥锁，而put操作释放先前获取的互斥锁。 为了使线程释放互斥锁，放置操作的数量必须等于先前的获取操作的数量。

每个互斥锁都是公共资源。 ThreadX对使用互斥体没有任何限制。

ThreadX互斥锁仅用于互斥。 与计数信号量不同，互斥锁不能用作事件通知的方法。



## API
- tx_mutex_create
- tx_mutex_delete
- tx_mutex_get
- tx_mutex_put

### tx_mutex_create

```c
UINT tx_mutex_create(TX_MUTEX *mutex_ptr,
    CHAR *name_ptr, UINT priority_inherit);
```

**参数**

- mutex_ptr指向互斥控制块的指针。
- name_ptr指向互斥器名称的指针。
- priority_inherit指定此互斥是否支持优先级继承。如果此值为TX_INHERIT，则支持优先级继承。但是，如果TX_NO_INHERIT，则此互斥不支持优先级继承。

**返回值**

- TX_SUCCESS （0x00） 成功创建静音。
- TX_MUTEX_ERROR （0x1C） 无效的互斥指针。指针为 NULL 或已创建互斥。
- TX_CALLER_ERROR服务（0x13）无效调用。
- TX_INHERIT_ERROR （0x1F） 无效优先级继承参数。



### tx_mutex_delete

```c
UINT tx_mutex_delete(TX_MUTEX *mutex_ptr);
```

**参数**

- mutex_ptr指向以前创建的互斥的指针。

**返回值**

- TX_SUCCESS （0x00） 成功静音删除。
- TX_MUTEX_ERROR （0x1C） 无效的互斥指针。
- TX_CALLER_ERROR服务（0x13）无效调用。

### tx_mutex_get

```c
UINT tx_mutex_get(TX_MUTEX *mutex_ptr, ULONG wait_option);
```

**参数**

- mutex_ptr 指向以前创建的互斥的指针。
- wait_option 定义如果互斥已被另一个线程拥有，服务的行为方式。等待选项的定义如下：
 1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从初始化调用服务，则这是唯一有效的选项。
2. TX_WAIT_FOREVER超时值 （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到互斥可用。超时值 （0x0000001 到 0xFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待互斥时保持挂起计时器的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功静音获取操作。
- TX_DELETED （0x01） Mutex 在线程挂起时被删除。
- TX_NOT_AVAILABLE （0x1D） 服务无法在指定时间内获得互斥的所有权以等待。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_MUTEX_ERROR （0x1C） 无效的互斥指针。
- TX_WAIT_ERROR （0x04） 在非线程的TX_NO_WAIT上指定了除其他帐户以外的等待选项。
- TX_CALLER_ERROR服务（0x13）无效调用。


### tx_mutex_put

```c
UINT tx_mutex_put(TX_MUTEX *mutex_ptr);
```

**参数**

- mutex_ptr指向以前创建的互斥的指针。

**返回值**

- TX_SUCCESS （0x00） 成功静音释放。
- TX_NOT_OWNED （0x1E） Mutex 不归呼叫者所有。
- TX_MUTEX_ERROR （0x1C） 到互斥的无效指针。
- TX_CALLER_ERROR服务（0x13）无效调用。

## mutex_demo
```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * mutex_demo.c
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

/*互斥量	临界区*/
TX_MUTEX mutex_lock;

UINT count_A = 0;
UINT count_B = 0;

/* Tracex使用 */
/*跟踪缓冲区的内存大小*/
#define trace_buffer_size 64000
/*要保留在跟踪注册表中的应用程序ThreadX对象的数量*/
#define registry_entries 40
UCHAR trace_buffer_start[trace_buffer_size];
UINT trace_status;
/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
/* USER CODE BEGIN PFP */
CHAR buffer[20]; //my_printf缓冲区 
void my_printf(CHAR *s,INT var);

/* USER CODE END PFP */

int main(void)
{
	.......
	/* USER CODE BEGIN 2 */

	tx_kernel_enter(); //threadx 入口

	/* USER CODE END 2 */
	while (1);
}
void MyThread_1_entry(ULONG entry_input)
{

	while (1)
	{
		/*临界区上锁*/
	//	tx_mutex_get(&mutex_lock,TX_WAIT_FOREVER);

		/*临界区 ...*/

		count_A ++;

		/* 让给线程2 使得count_A & count_B 不同步 */
		tx_thread_sleep(1000);

		count_B ++;
		/*临界区释放锁*/
	//	tx_mutex_put(&mutex_lock);

	}
}

void MyThread_2_entry(ULONG entry_input)
{

	while (1)
	{
		/*临界区上锁*/
	//	tx_mutex_get(&mutex_lock,TX_WAIT_FOREVER);

		/*临界区 ...*/

		if(count_A == count_B)
		{
			my_printf("count_A = count_B", HAL_MAX_DELAY);
		}
		else
		{
			my_printf("count_A != count_B", HAL_MAX_DELAY);
		}

		count_A ++;
		count_B ++;

		/*临界区释放锁*/
	//	tx_mutex_put(&mutex_lock);

		tx_thread_sleep(500);
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
			3,//优先级3 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,//禁用抢占的最高优先级
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
			1,//优先级1 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);

	/*创建互斥量 临界区 */
	tx_mutex_create(&mutex_lock,"mutex_lock",TX_NO_INHERIT);

}

void my_printf(CHAR *s,INT var)
{
	if(var == HAL_MAX_DELAY)
	{
		sprintf(buffer,s);
		HAL_UART_Transmit(&huart1, (uint8_t*)buffer, sizeof(buffer), HAL_MAX_DELAY);
	}
	else
	{
		sprintf(buffer,s,var);
		HAL_UART_Transmit(&huart1, (uint8_t*)buffer, sizeof(buffer), HAL_MAX_DELAY);
	}
}
/* USER CODE END 4 */

```

没有使用互斥量的时候，在线程1 tx_thread_sleep(1000); 挂起的时候,线程2操作变量使得 count_A 与 count_B 不同步

![](https://img-blog.csdnimg.cn/20200827192012559.png#pic_center)

![](https://img-blog.csdnimg.cn/20200827195035956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)


使用互斥量，保护共享资源，防止其它线程的破坏

![](https://img-blog.csdnimg.cn/20200827192548651.png#pic_center)

![](https://img-blog.csdnimg.cn/20200827194558535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

完
