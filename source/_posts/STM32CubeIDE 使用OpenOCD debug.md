---
title: STM32CubeIDE & OpenOCD  
author: henji
img: #/source/images/xxx.jpg
top: false
cover: #true
coverImg: #/images/1.jpg
password: #8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
toc: true
mathjax: false
summary: STM32CubeIDE & OpenOCD  
categories: STM32
tags:
  - STM32CubeIDE
  - OpenOCD
date: 2020/7/29 13:48:25
---
## 问题
针对手头的正点原子潘多拉(Pandora IoT)开发板在使用STM32CubeIDE时，提示ST-Link固件升级，不能下载，更不能debug。
![](https://img-blog.csdnimg.cn/20200809190941888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_1,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_10,color_FFFFFF,t_50)
![](https://img-blog.csdnimg.cn/20200809191022401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_20,color_000000,t_90)
## 解决

1.方法一：
* 固件升级这里可以参考：
[暴力升级你的 ST-Link 及 STM32CubeIDE](https://blog.csdn.net/arminkztl/article/details/98382536)

2.方法二
* 使用OpenOCD
首先安装OpenOCD(网上一大堆，不赘诉）
[点击下载：OpenOCD](https://download.csdn.net/download/qq_37555002/12699689)
---
- cfg文件：

Pandora的芯片时STM32L475VET6
所以这里选择比较接近的stm32L4discovery.cfg
![](https://img-blog.csdnimg.cn/20200809192229296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
最关键的两个文件 stlink.cfg & stm32l4x.cfg
说明stm32l4系列单片机和st-link调试大概都是这样设置的，其它型号以及调试自行组合(里面cfg那么多找个自己跟着改一改)

这样仿照着为Pandora 写一个 stm32l4Pandora.cfg
 ![](https://img-blog.csdnimg.cn/20200809194047736.png)

```c
source [find interface/stlink.cfg]

transport select hla_swd

source [find target/stm32l4x.cfg]

# reset_config srst_only

```

最后一行重置有可能会影响后面的调试，没有影响的就不用注释，有影响就试试注释看一下能不能解决。

**st-link 连接PC，启动OpenOCD**
- openocd -f  "绝对路径.cfg文件"（默认路径在board/下）
```c
openocd -f "board/stm32l4Pandora.cfg" 
```
![](https://img-blog.csdnimg.cn/20200809194945254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
可以看到已经识别到了st-link 相关信息。

**打开STM32CubeIDE 工程**
 - 主函数随便写几行
```c
int main(void)
{
	/* USER CODE BEGIN 1 */
	uint16_t count = 0;
	uint8_t pData[] = "hello mcu stm32 !\n";
	uint8_t pData1[] = "I am pData1\n";
	uint8_t pData2[] = "I am pData2\n";
	/* USER CODE END 1 */
	...
	...
	...
	/* USER CODE BEGIN 2 */
	HAL_UART_Transmit(&huart1, pData, sizeof(pData), HAL_MAX_DELAY);
	HAL_Delay(1000);
	HAL_UART_Transmit(&huart1, pData1, sizeof(pData1), HAL_MAX_DELAY);
	HAL_Delay(1000);
	HAL_UART_Transmit(&huart1, pData2, sizeof(pData2), HAL_MAX_DELAY);
	/* USER CODE END 2 */

	/* Infinite loop */
	/* USER CODE BEGIN WHILE */
	while (1)
	{
		count++;
		HAL_Delay(1000);
		/* USER CODE END WHILE */

		/* USER CODE BEGIN 3 */
	}
	/* USER CODE END 3 */
}
```
- **配置OpenOCD**
1. 首先找到Run configurations
![](https://img-blog.csdnimg.cn/20200809195630632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20200809195528470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
 2. 选择 ST-LINK(OpenOCD)
![](https://img-blog.csdnimg.cn/20200809200115593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20200809200206619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20200809200324463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
这样就烧写进去了
![](https://img-blog.csdnimg.cn/20200809200535761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
3.  点击debug
![](https://img-blog.csdnimg.cn/2020080920075422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20200809201150475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTU1MDAy,size_16,color_FFFFFF,t_70)
完

