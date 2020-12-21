---
title: ThreadX(五)------内存管理
author: henji
img: 
top: false
# cover: true
coverImg: 
password: 
toc: true
mathjax: false
summary: ThreadX(五)------内存管理
categories: ThreadX
tags:
  - ThreadX
date: 2020-08-26 18:06:46
---

## API
---
- tx_block_allocate
- tx_block_pool_create
- tx_block_pool_delete
- tx_block_pool_info_get
- tx_block_pool_performance_info_get
- tx_block_pool_performance_system_info_get
- tx_block_pool_prioritize
- tx_block_release
---
- tx_byte_allocate
- tx_byte_pool_create
- tx_byte_pool_delete
- tx_byte_pool_info_get
- tx_byte_pool_performance_info_get
- tx_byte_pool_performance_system_info_get
- tx_byte_pool_prioritize
- tx_byte_release

---

### tx_block_allocate

```c
UINT tx_block_allocate(TX_BLOCK_POOL *pool_ptr, VOID **block_ptr,
    ULONG wait_option);
```

**参数**

- pool_ptr 指向以前创建的内存块池的指针。
- block_ptr 指向目标块指针的指针。成功分配时，分配的内存块的地址将放置在此参数点的位置。
- wait_option 定义服务在没有可用的内存块时的行为方式。
等待选项的定义如下：
1. TX_NO_WAIT （0x0x0000000） - TX_NO_WAIT，无论它成功与否，都会立即从该服务返回。如果从非线程调用服务，则这是唯一有效的选项;例如，初始化、计时器或 ISR。
2. TX_WAIT_FOREVER （0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到内存块可用。
超时值（0x0000001 到 0xFFFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待内存块时保持挂起计时器的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功的内存块分配。
- TX_DELETED时，已删除内存块池 （0x01） 内存块池。
- TX_NO_MEMORY （0x10） 服务无法在指定时间内分配内存块以等待。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_POOL_ERROR （0x02） 无效内存块池指针。
- TX_WAIT_ERROR （0x04） 在从非TX_NO_WAIT的呼叫上指定的等待选项（非线程） 以外的等待选项。
- TX_PTR_ERROR （0x03） 到目标指针的无效指针。


### tx_block_pool_create

```c
UINT tx_block_pool_create(TX_BLOCK_POOL pool_ptr,
  CHAR name_ptr, ULONG block_size,
  VOID pool_start, ULONG pool_size);
```

**参数**

- pool_ptr指向内存块池控件块的指针。
- name_ptr指向内存块池的名称的指针。
- block_size每个内存块中的字节数。
- pool_start内存块池的起始地址。起始地址必须与 ULONG 数据类型的大小对齐。
- pool_size内存块池可用的字节总数。

**返回值**

- TX_SUCCESS （0x00） 成功创建内存块池。
- TX_POOL_ERROR （0x02） 无效内存块池指针。指针为 NULL 或已创建池。
- TX_PTR_ERROR （0x03） 池的无效起始地址。
- TX_CALLER_ERROR服务（0x13）无效调用方。
- TX_SIZE_ERROR （0x05） 池的大小无效。

### tx_block_pool_delete

```c
UINT tx_block_pool_delete(TX_BLOCK_POOL *pool_ptr);
```

-**参数** 

- pool_ptr指向以前创建的内存块池的指针。

**返回值**

- TX_SUCCESS （0x00） 成功删除内存块池。
- TX_POOL_ERROR （0x02） 无效内存块池指针。
- TX_CALLER_ERROR服务（0x13）无效调用方。


### tx_block_release

```c
UINT tx_block_release(VOID *block_ptr);
```

**参数**

-  block_ptr指向以前分配的内存块的指针。

**返回值**

- TX_SUCCESS （0x00） 成功释放内存块。
- TX_PTR_ERROR （0x03） 内存块的无效指针。


---

### tx_byte_allocate

```c
UINT tx_byte_allocate(TX_BYTE_POOL *pool_ptr,
  VOID **memory_ptr, ULONG memory_size,
  ULONG wait_option);
```
**参数**

- pool_ptr   指向以前创建的内存块池的指针。
- memory_ptr 指向目标内存指针的指针。成功分配时，分配的内存区域的地址将放置在此参数指向的位置。
- memory_size 请求的字节数。
- wait_option 定义服务在可用内存不足时的行为方式。
等待选项的定义如下：
1. TX_NO_WAIT （0x00000000） - TX_NO_WAIT，无论它是否成功，都会立即从该服务返回。如果从初始化调用服务，这是唯一有效的选项。
2. TX_WAIT_FOREVER 0xFFFF） - TX_WAIT_FOREVER，这将导致调用线程无限期挂起，直到有足够的内存可用。
超时值（0x0000001 到 0xFFFFFE） - 选择数值 （1-0xFFFFFFFE） 指定在等待内存时保持挂起计时器刻度的最大数量。

**返回值**

- TX_SUCCESS （0x00） 成功分配内存。
- TX_DELETED时，已删除内存池 （0x01） 内存池。
- TX_NO_MEMORY （0x10） 服务无法在指定时间内分配内存以等待。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_POOL_ERROR （0x02） 无效内存池指针。
- TX_PTR_ERROR （0x03） 到目标指针的无效指针。
- TX_SIZE_ERROR （0X05） 请求的大小为零或大于池。
- TX_WAIT_ERROR （0x04） 在从非TX_NO_WAIT的呼叫上指定的等待选项（非线程） 以外的等待选项。
- TX_CALLER_ERROR服务（0x13）无效调用方。

### tx_byte_pool_create

```c
UINT tx_byte_pool_create(TX_BYTE_POOL *pool_ptr,
  CHAR *name_ptr, VOID *pool_start,
  ULONG pool_size);
```

**参数**

* pool_ptr指向内存池控件块的指针。
* name_ptr指向内存池名称的指针。
* pool_start内存池的起始地址。起始地址必须与 ULONG 数据类型的大小对齐。
* pool_size可用于内存池的字节总数。

**返回值**

* TX_SUCCESS （0x00） 成功创建内存池。
* TX_POOL_ERROR （0x02） 无效内存池指针。指针为 NULL 或已创建池。
* TX_PTR_ERROR （0x03） 池的无效起始地址。
* TX_SIZE_ERROR （0x05） 池的大小无效。
* TX_CALLER_ERROR服务（0x13）无效调用方。

### tx_byte_pool_delete

```c
UINT tx_byte_pool_delete(TX_BYTE_POOL *pool_ptr);
```
**参数**

- pool_ptr指向以前创建的内存池的指针。

**返回值**

- TX_SUCCESS （0x00） 成功删除内存池。
- TX_POOL_ERROR （0x02） 无效内存池指针。
- TX_CALLER_ERROR服务（0x13）无效调用方。

### tx_byte_release

```c
UINT tx_byte_release(VOID *memory_ptr);
```

**参数**

- memory_ptr指向以前分配的内存区域的指针。

**返回值**

- TX_SUCCESS （0x00） 成功释放内存。
- TX_PTR_ERROR （0x03） 无效内存区域指针。
- TX_CALLER_ERROR服务（0x13）无效调用方。

### memory_demo
```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * tracex_demo.c
 * Change Logs:
 * Date		Author		Notes
 * 2020年8月25日	henji		the first version
 */
	.......
	.......
/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
/* USER CODE END Includes */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */
TX_THREAD my_thread_1;
TX_THREAD my_thread_2;
TX_THREAD trace_thread;
uint8_t pData[] = "=========ThreadX=========\n";
uint8_t pData1[] = "I am thread1 ";
uint8_t pData2[] = "I am thread2 ";


/*线程栈大小*/
#define DEMO_STACK_SIZE 1024
/*内存池总大小*/
#define DEMO_BYTE_POOL_SIZE 1024*5
/*内存块池总大小*/
#define DEMO_BLOCK_POOL_SIZE 100
/*内存字节池控制指针*/
TX_BYTE_POOL byte_pool_0;
/*内存块池控制指针*/
TX_BLOCK_POOL block_pool_0;
/* 指向内存的指针 */
UCHAR *memory_ptr;

/* Tracex使用 */
/*跟踪缓冲区的内存大小*/
#define trace_buffer_size 64000
/*要保留在跟踪注册表中的应用程序ThreadX对象的数量*/
#define registry_entries 40
UCHAR trace_buffer_start[trace_buffer_size];
UINT trace_status;
/* USER CODE END PV */
	.......
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
		if (count == 0)
		{
			HAL_UART_Transmit(&huart1, init_data, sizeof(init_data),
			HAL_MAX_DELAY);
		}
		else
		{
			HAL_UART_Transmit(&huart1, pData1, sizeof(pData1), HAL_MAX_DELAY);
		}
		count++;
		HAL_GPIO_TogglePin(GPIOE, GPIO_PIN_7 | GPIO_PIN_8);
		tx_thread_sleep(30);
	}
}

void thread2_entry(ULONG entry_input)
{
	INT count = 0;
	while (1)
	{
		HAL_UART_Transmit(&huart1, pData2, sizeof(pData2), HAL_MAX_DELAY);
		if (count == 1)
		{
			/*分配一个内存块空间*/
			 tx_byte_allocate(&byte_pool_0,		   //内存池的指针
					 	 	  (VOID **)&memory_ptr,//指向目标内存指针的指针
							  DEMO_BLOCK_POOL_SIZE,//分配内存块区域
							  TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
							  );
			 /*创建内存块*/
			 tx_block_pool_create(&block_pool_0, 	 //内存块池的指针
					 	 	 	 "block pool 0", 	 //内存块池名称
								 10,			 	 //每块大小
								 memory_ptr,     	 //指向目标内存指针的指针
								 DEMO_BLOCK_POOL_SIZE//内存块池总字节数
								 );
			 /*分配内存块*/
			 tx_block_allocate(&block_pool_0, //内存块池的指针
					  (VOID **)&memory_ptr,   //指向目标内存指针的指针
							   TX_NO_WAIT //  无论它成功与否，都会立即从该服务返回
			 	 	 	 	   );
			 /*释放*/
			 tx_block_release(memory_ptr);

		}
		count++;

		tx_thread_sleep(20);
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
		tx_thread_sleep(10);
	}
}


void tx_application_define(void *first_unused_memory)
{

	/* 追踪 */
	trace_status = tx_trace_enable(&trace_buffer_start, trace_buffer_size, registry_entries);
	/*创建一个内存池用于分配线程栈*/
	tx_byte_pool_create(&byte_pool_0, 		//内存池的指针
					"byte pool 0",		//名称
					first_unused_memory,//分配内存地址
					DEMO_BYTE_POOL_SIZE //分配内存池大小
					);

	/*分配一个栈空间用于trace*/
	 tx_byte_allocate(&byte_pool_0,		   //内存池的指针
			 	 	  (VOID **)&memory_ptr,//指向目标内存指针的指针
					  DEMO_STACK_SIZE,     //分配栈大小
					  TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
					  );

	/*trace 线程*/
	tx_thread_create(&trace_thread,	//线程控制块指针
			"trace_thread",         //线程名字
			trace_thread_input,//线程入口函数
			0,               //线程入口参数
			memory_ptr,		 //线程的起始地址
			DEMO_STACK_SIZE, //内存区域大小K
			1,               //优先级2 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,               //禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START    //线程自动启动
			);

	/*分配一个栈空间用于线程1*/
	 tx_byte_allocate(&byte_pool_0,		   //内存池的指针
			 	 	  (VOID **)&memory_ptr,//指向目标内存指针的指针
					  DEMO_STACK_SIZE,     //分配栈大小
					  TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
					  );

	/*创建线程1*/
	tx_thread_create(&my_thread_1,	//线程控制块指针
			"my_thread1",    //线程名字
			thread1_entry,   //线程入口函数
			0,				 //线程入口参数
			memory_ptr,		 //线程的起始地址
			DEMO_STACK_SIZE, //线程栈大小 K
			3,				 //优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			3,				 //禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START    //线程自动启动
			);

	/*分配一个栈空间用于线程2*/
	 tx_byte_allocate(&byte_pool_0,		   //内存池的指针
			 	 	  (VOID **)&memory_ptr,//指向目标内存指针的指针
					  DEMO_STACK_SIZE,     //分配栈大小
					  TX_NO_WAIT		   //无论它是否成功，都会立即从该服务返回
					  );
	/*线程2*/
	tx_thread_create(&my_thread_2,	//线程控制块指针
			"my_thread2",    //线程名字
			thread2_entry,   //线程入口函数
			0,				 //线程入口参数
			memory_ptr,      //线程的起始地址
			DEMO_STACK_SIZE, //线程栈大小 K
			2,               //优先级2 (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			2,               //禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START    //线程自动启动
			);

}
/* USER CODE END 4 */

```

![](https://img-blog.csdnimg.cn/20200822230207756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

完
