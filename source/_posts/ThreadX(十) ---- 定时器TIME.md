---
title: ThreadX(十)---定时器TIME
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: ThreadX(十)------定时器TIME
categories: ThreadX
tags:
  - ThreadX
  - STM32
date: 2020-08-28 22:07:21
---



## API
- tx_timer_create
- tx_timer_delete
- tx_timer_activate
- tx_timer_change
- tx_timer_deactivate

### tx_timer_create

```c
UINT tx_timer_create(TX_TIMER *timer_ptr, CHAR *name_ptr,
    VOID (*expiration_function)(ULONG),
    ULONG expiration_input, ULONG initial_ticks,
    ULONG reschedule_ticks, UINT auto_activate);
```

**参数**

- timer_ptr 指向计时器控制块的指针
- name_ptr 指向计时器名称的指针。
- expiration_function 当计时器过期时要调用的应用程序函数。
- expiration_input 当计时器过期时，输入将传递到过期函数。
- initial_ticks 指定计时器过期的初始刻度数。值的范围为 1 到 0xFFFF。
- reschedule_ticks 指定第一个计时器过期后的所有计时器的刻度数。此参数的零使计时器成为一次计时器。否则，对于定期计时器，合法值的范围为 1 到 0xFFFF。
-  注意 一次计时器过期后，必须通过一个tx_timer_change重置，然后才能再次激活。
- auto_activate 确定计时器在创建过程中是否自动激活。如果此值为
 1. TX_AUTO_ACTIVATE （0x01），则计时器将处于活动状态。否则，如果选择了
 2. TX_NO_ACTIVATE （0x00） 的值，则计时器将创建为非活动状态。在这种情况下，需要进行tx_timer_activate服务调用，才能实际启动计时器。

**返回值**

- TX_SUCCESS （0x00） 成功创建应用程序计时器。
- TX_TIMER_ERROR （0x15） 无效应用程序计时器指针。指针为 NULL 或已创建计时器。
- TX_TICK_ERROR报价提供的无效值 （0x16） 无效值 （零）。
- TX_ACTIVATE_ERROR （0x17） 无效激活。
- TX_CALLER_ERROR服务（0x13）无效调用。


### tx_timer_delete

```c
UINT tx_timer_delete(TX_TIMER *timer_ptr);
```

**参数**

- timer_ptr 指向以前创建的应用程序计时器的指针。

**返回值**

- TX_SUCCESS （0x00） 成功删除应用程序计时器。
- TX_TIMER_ERROR （0x15） 无效应用程序计时器指针。
- TX_CALLER_ERROR服务（0x13）无效调用。

### tx_timer_activate

```c
UINT tx_timer_activate(TX_TIMER *timer_ptr);
```

**参数**

- timer_ptr 指向以前创建的应用程序计时器的指针。

**返回值**

- TX_SUCCESS （0x00） 成功激活应用程序计时器。
- TX_TIMER_ERROR （0x15） 无效应用程序计时器指针。
- TX_ACTIVATE_ERROR （0x17） 计时器已处于活动状态，或者是已过期的一次计时器。

### tx_timer_change

```c
UINT tx_timer_change(TX_TIMER *timer_ptr,
    ULONG initial_ticks, ULONG reschedule_ticks);
```

**参数**
- timer_ptr 指向计时器控制块的指针。
- initial_ticks 指定计时器过期的初始刻度数。值的范围为 1 到 0xFFFF。
- reschedule_ticks 指定第一个计时器过期后的所有计时器的刻度数。此参数的零使计时器成为一次计时器。否则，对于定期计时器，合法值的范围为 1 到 0xFFFF。
-  注意 过期的一次定时器必须通过tx_timer_change，然后才能再次激活。

**返回值**

- TX_SUCCESS （0x00） 成功的应用程序计时器更改。
- TX_TIMER_ERROR （0x15） 无效应用程序计时器指针。
- TX_TICK_ERROR报价提供的无效值 （0x16） 无效值 （零）。
- TX_CALLER_ERROR服务（0x13）无效调用。
- 
### tx_timer_deactivate

```c
UINT tx_timer_deactivate(TX_TIMER *timer_ptr);
```

**参数**

- timer_ptr指向以前创建的应用程序计时器的指针。

**返回值**

- TX_SUCCESS （0x00） 成功应用计时器停用。
- TX_TIMER_ERROR （0x15） 无效应用程序计时器指针。

## TIME_demo

```c
/*
 *
 * SPDX-License-Identifier: GPL-2.0-or-later
 * TIME_demo.c
 * Change Logs:
 * Date           Author       Notes
 * 2020年8月28日     	  henji      the first version
 */

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "tx_api.h"
#include "stdio.h"
	........
/* 软定时器 */
TX_TIMER timer_1;
TX_TIMER timer_2;
	........
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
	........
	/* USER CODE BEGIN 2 */

	tx_kernel_enter(); //threadx 入口

	/* USER CODE END 2 */

	/* Infinite loop */
	/* USER CODE BEGIN WHILE */
	while (1);
}

void Timer_1_entry(ULONG expiration_input)
{
	HAL_UART_Transmit(&huart1, (uint8_t *)"I am timer 1 ", sizeof("I am timer 1 "), HAL_MAX_DELAY);
}

void Timer_2_entry(ULONG expiration_input)
{
	HAL_UART_Transmit(&huart1, (uint8_t *)"I am timer 2 ", sizeof("I am timer 2 "), HAL_MAX_DELAY);
}

void tx_application_define(void *first_unused_memory)
{
	/*使能追踪*/
	trace_status = tx_trace_enable(&trace_buffer_start, trace_buffer_size,registry_entries);

	/* 创建定时器1 */
	tx_timer_create(&timer_1,      //定时器控制块
					"timer 1",     //定时器名称
					Timer_1_entry, //定时器入口函数
					0,   //定时器入口参数
					500, //定时器初始定时 500 Ticks
					500, //定时器重载500 Ticks (0 ticks 一次性定时器   )
					TX_AUTO_ACTIVATE //自动激活
					);

	/* 创建定时器2 */
	tx_timer_create(&timer_2,      //定时器控制块
					"timer 2",     //定时器名称
					Timer_2_entry, //定时器入口函数
					0,   //定时器入口参数
					100, //定时器初始定时 500 Ticks
					100, //定时器重载500 Ticks (0 ticks 一次性定时器   )
					TX_AUTO_ACTIVATE //自动激活
					);

}

```


![](https://img-blog.csdnimg.cn/20200828214845696.png#pic_center)

完