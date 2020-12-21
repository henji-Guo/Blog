---
title: ThreadX(三) ------ 线程thread
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(三) ------ 线程thread
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020/8/14 13:48:25
---

## 常用API
- tx_thread_create
- tx_thread_delete
- tx_thread_preemption_change
- tx_thread_priority_change
- tx_thread_relinquish
- tx_thread_reset
- tx_thread_resume
- tx_thread_sleep
- tx_thread_suspend
- tx_thread_terminate
- tx_thread_wait_abort 

## tx_thread_create

```c
UINT tx_thread_create(TX_THREAD *thread_ptr,
  CHAR *name_ptr, VOID (*entry_function)(ULONG),
  ULONG entry_input, VOID *stack_start,
  ULONG stack_size, UINT priority,
  UINT preempt_threshold, ULONG time_slice,
  UINT auto_start);
```
- thread_ptr指向线程控件块的指针。
- name_ptr指向线程名称的指针。
- entry_function指定线程执行的初始 C 函数。当线程从此条目函数返回时，它将处于已完成状态并无限期挂起。
- entry_input线程首次执行时传递给线程的输入函数的 32 位值。此输入的使用完全由应用程序决定。
- stack_start堆栈内存区域的起始地址。
- stack_size堆栈内存区域中的字节数。线程的堆栈区域必须足够大，以处理其最坏情况下的函数调用嵌套和本地变量使用。
- 优先级线程的数字优先级。法律值的范围为 0 到 TX_MAX_PRIORITES-1，其中值 0 表示最高优先级。
- preempt_threshold禁用抢占的最高优先级 （0 到 TX_MAX_PRIORITIES-1））。只有高于此级别的优先级才能抢占此线程。此值必须小于或等于指定的优先级。等于线程优先级的值禁用抢占阈值。
- time_slice允许此线程在获得运行机会之前运行此线程的计时器刻度数。请注意，使用抢占阈值可禁用时间切片。法定时间切片值范围为 1 到 0xFFFF（含）。值TX_NO_TIME_SLICE（值为 0）将禁用此线程的时间切片。
- auto_start指定线程是立即启动还是处于挂起状态。法律选项包括TX_AUTO_START （0x01） 和TX_DONT_START （0x00）。如果TX_DONT_START，应用程序稍后必须调用tx_thread_resume才能运行线程。

**返回值**
- TX_SUCCESS （0x00） 成功创建线程。
- TX_THREAD_ERROR （0x0E） 无效线程控制指针。指针为 NULL 或线程已创建。
- TX_PTR_ERROR （0x03） 入口点或堆栈区域的无效起始地址无效，通常为 NULL。
- TX_SIZE_ERROR （0x05） 堆栈区域的大小无效。线程必须至少具有TX_MINIMUM_STACK字节。
- TX_PRIORITY_ERROR （0x0F） 无效线程优先级，这是超出范围 （0 到 （TX_MAX_PRIORITIES-1） 的值。
- TX_THRESH_ERROR （0x18） 无效抢占指定。此值必须是小于或等于线程的初始优先级的有效优先级。
- TX_START_ERROR （0x10） 自动启动选择无效。
- TX_CALLER_ERROR服务（0x13）无效调用。
  
## tx_thread_delete

```c
UINT tx_thread_delete(TX_THREAD *thread_ptr);
```
- thread_ptr指向以前创建的应用程序线程的指针。

**返回值**
- TX_SUCCESS （0x00） 成功删除线程。
- TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_DELETE_ERROR （0x11） 指定的线程未处于终止或已完成状态。
- TX_CALLER_ERROR服务（0x13）无效调用。


## tx_thread_preemption_change

```c
UINT tx_thread_preemption_change(TX_THREAD *thread_ptr,
    UINT new_threshold, UINT *old_threshold);
```

 - thread_ptr指向以前创建的应用程序线程的指针。
 - new_threshold新的抢占阈值优先级（0 到 （TX_MAX_PRIORITIES-1）。
-  old_threshold指向位置的指针以返回上一个抢占阈值。

**返回值**

 - TX_SUCCESS （0x00） 成功抢占阈值更改。
 - TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
 - TX_THRESH_ERROR （0x18） 指定的新抢占阈值不是有效的线程优先级（0 到   （TX_MAX_PRIORITIES-1））以外的值，或大于（优先级较低）的当前线程优先级。
 - TX_PTR_ERROR （0x03） 无效指针指向以前的抢占保留存储位置。
 - TX_CALLER_ERROR服务（0x13）无效调用方。

## tx_thread_priority_change

```c
UINT tx_thread_priority_change(TX_THREAD *thread_ptr,
    UINT new_priority, UINT *old_priority);
```
- thread_ptr指向以前创建的应用程序线程的指针。
- new_priority新线程优先级（0 到 （TX_MAX_PRIORITIES-1）。
- old_priority指向位置的指针以返回线程的上一个优先级。

**返回值**
- TX_SUCCESS （0x00） 成功优先级更改。
- TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_PRIORITY_ERROR （0x0F） 指定的新优先级无效（0 到TX_MAX_PRIORITIES-1） 以外的值）。
- TX_PTR_ERROR （0x03） 指向上一个优先级存储位置的无效指针。
- TX_CALLER_ERROR服务（0x13）无效调用。


## tx_thread_relinquish

```c
VOID tx_thread_relinquish(VOID);
```
以相同或更高的优先级将处理器控制放弃给其他随时可以运行的线程。


## tx_thread_reset

```c
UINT tx_thread_reset(TX_THREAD *thread_ptr);
```

- thread_ptr指向以前创建的线程的指针。

**返回值**

 - TX_SUCCESS （0x00） 成功重置线程。

- TX_NOT_DONE （0x20） 指定的线程未处于TX_COMPLETED或者TX_TERMINATED状态。
- TX_THREAD_ERROR （0x0E） 无效线程指针。
- TX_CALLER_ERROR服务（0x13）无效调用。


## tx_thread_resume

```c
UINT tx_thread_resume(TX_THREAD *thread_ptr);
```
- thread_ptr指向挂起的应用程序线程的指针。

**返回值**
- TX_SUCCESS （0x00） 成功线程恢复。
- TX_SUSPEND_LIFTED （0x19） 先前设置的延迟悬挂已解除。
- TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_RESUME_ERROR （0x12） 指定的线程未挂起，或以前由服务挂起，tx_thread_suspend。

## tx_thread_sleep

```c
UINT tx_thread_sleep(ULONG timer_ticks);
```
- timer_ticks挂起调用应用程序线程的计时器数，范围从 0 到 0xFFFF。如果指定了 0，服务将立即返回。

**返回值**
- TX_SUCCESS （0x00） 成功的线程睡眠。
- TX_WAIT_ABORTED （0x1A） 挂起被另一个线程、计时器或 ISR 中止。
- TX_CALLER_ERROR （0x13） 服务从非线程调用。

## tx_thread_suspend

```c
UINT tx_thread_suspend(TX_THREAD *thread_ptr);
```
- thread_ptr指向应用程序线程的指针。

**返回值**
-  TX_SUCCESS （0x00） 成功线程挂起。
-  TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_SUSPEND_ERROR （0x14） 指定的线程处于终止或已完成状态。
- TX_CALLER_ERROR服务（0x13）无效调用。

## tx_thread_terminate

```c
UINT tx_thread_terminate(TX_THREAD *thread_ptr);
```
- thread_ptr指向应用程序线程的指针。

**返回值**
- TX_SUCCESS （0x00） 成功线程终止。
- TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_CALLER_ERROR服务（0x13）无效调用方。

## tx_thread_wait_abort 

```c
UINT tx_thread_time_slice_change(TX_THREAD *thread_ptr,
    ULONG new_time_slice, ULONG *old_time_slice);
```

- thread_ptr指向以前创建的应用程序线程的指针。

**返回值**
- TX_SUCCESS （0x00） 成功线程等待中止。
- TX_THREAD_ERROR （0x0E） 无效的应用程序线程指针。
- TX_WAIT_ABORT_ERROR （0x1B） 指定的线程未处于等待状态。

## threadx_demo

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 *
 * Change Logs:
 * Date		Author		Notes
 * 2020年8月15日	henji		the first version
 */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "spi.h"
#include "tim.h"
#include "usart.h"
#include "gpio.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
/* USER CODE END Includes */
	......
/* USER CODE BEGIN PV */
TX_THREAD my_thread_1;
TX_THREAD my_thread_2;
uint8_t pData[] = "=========ThreadX=========\n";
uint8_t pData1[] = "I am thread1 ";
uint8_t pData2[] = "I am thread2 ";
/* USER CODE END PV */
	......	
/**
 * @brief  The application entry point.
 * @retval int
 */
int main(void)
{
	......
	
	/* USER CODE BEGIN 2 */

	tx_kernel_enter(); //threadx 入口

	/* USER CODE END 2 */
	/* Infinite loop */
	/* USER CODE BEGIN WHILE */
	while (1);
	/* USER CODE END 3 */
}

/* USER CODE BEGIN 4 */
void thread1_entry(ULONG entry_input)
{

	INT count = 0;
	uint8_t init_data[]="start now";
	while (1)
	{

		HAL_UART_Transmit(&huart1, pData1, sizeof(pData1), HAL_MAX_DELAY);
		if(count == 0)
		{
			HAL_UART_Transmit(&huart1, init_data, sizeof(init_data), HAL_MAX_DELAY);
		}
		count++;
		//HAL_GPIO_TogglePin(GPIOE, GPIO_PIN_7|GPIO_PIN_8);

		tx_thread_sleep(1000); // 线程睡眠1000 timer_ticks

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
			/*终止线程1-2*/
			tx_thread_terminate(&my_thread_1);
			tx_thread_terminate(&my_thread_2);
		}
		else
		{
			;
		}
		count++;
		tx_thread_sleep(1000); // 线程睡眠500 timer_ticks
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
		//HAL_GPIO_TogglePin(GPIOE, GPIO_PIN_7|GPIO_PIN_8|GPIO_PIN_9);
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
			1,//优先级3  (0~TX_MAX_PRIORITES-1)0  表示最高优先级
			1,//禁用抢占的最高优先级
			TX_NO_TIME_SLICE,//时间切片值范围为 1 ~ 0xFFFF(TX_NO_TIME_SLICE = 0)
			TX_AUTO_START//线程自动启动
			);
	/*线程进入和退出时通知*/
	tx_thread_entry_exit_notify(&my_thread_1, my_entry_exit_notify);
}
/* USER CODE END 4 */

	......
```
![](https://img-blog.csdnimg.cn/20200816174331664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70#pic_center)

