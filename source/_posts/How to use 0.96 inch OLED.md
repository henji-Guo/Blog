---
title: How to use u8g2
author: henji
img: 
top: false
cover: false
coverImg: 
password: 
toc: true
mathjax: false
summary: Using 0.96 OLED
categories: ESP32
tags:
  - ESP32
  - Arduino
date: 2020-11-15 00:07:36
---

## U8g2 Library
### 1. Click the Manage Libraries
![](https://img-blog.csdnimg.cn/20201115223757864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)

### 2. Installing the U8g2 Library
![](https://img-blog.csdnimg.cn/2020111522404430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
![](https://img-blog.csdnimg.cn/20201115224136969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
## Example
![](https://img-blog.csdnimg.cn/20201115224418615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
![](https://img-blog.csdnimg.cn/20201115224446233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
- 0.96 inch OLED can support two protocols which SPI and IIC*/
- Unfortunately , only support IIC that we have .


![](https://img-blog.csdnimg.cn/20201115225347728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
- Ctrl + F ,  input "ESP32" to find
![](https://img-blog.csdnimg.cn/20201115225739540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
- remove comments ，modify PIN
![](https://img-blog.csdnimg.cn/20201115230147575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)

- The most important 
![](https://img-blog.csdnimg.cn/20201115235245301.png#pic_center)

## PIN
![](https://img-blog.csdnimg.cn/20201115233436159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
##  Code
```c
/*
  ==================================================================================

  SPDX-License-Identifier: GPL-3.0-or-later

  File_name : OLED.ino
  Describe  : Using IIC to connect OLED
  Author    : GHJ
  Date      : 2020年11月16日 23:00:00

  PS:
     需要u8g2->u8g2库:https://github.com/olikraus/u8g2.git
  Change Logs:
  Date               Author          Notes
  2020年11月15日       GHJ         the first version

  ===================================================================================
*/

#include <Arduino.h>
#include <U8g2lib.h>

/*0.96 inch OLED can support two protocols which SPI and IIC*/
/*Unfortunately , only support IIC that we have .*/
#define U8X8_HAVE_HW_I2C
#ifdef U8X8_HAVE_HW_SPI
#include <SPI.h>
#endif
#ifdef U8X8_HAVE_HW_I2C
#include <Wire.h>
#endif

/*
  U8g2lib Example Overview:
    Frame Buffer Examples: clearBuffer/sendBuffer. Fast, but may not work with all Arduino boards because of RAM consumption
    Page Buffer Examples: firstPage/nextPage. Less RAM usage, should work with all Arduino boards.
    U8x8 Text Only Example: No RAM usage, direct communication with display controller. No graphics, 8x8 Text only.

  This is a page buffer example.
*/

/*       Interconnect

   ESP32  <--PIN-->   0.96 inch OLED
   G22    <------->   SCL
   G21    <------->   SDA
   3.3V   <------->   VCC
   GND    <------->   GND

*/
/* the most important that using all buffer */ 
U8G2_SSD1306_128X64_NONAME_F_HW_I2C u8g2(U8G2_R0, /* reset=*/ U8X8_PIN_NONE, /* clock=*/ 22, /* data=*/ 21);   // ESP32 Thing, HW I2C with pin remapping


void setup(void) {

  /* Using u8g2 */
  u8g2.begin();
  /* Allow u8g2 print UTF-8 front */
  u8g2.enableUTF8Print();
}

void loop(void) {

  u8g2.clearBuffer(); //clear screen

  /*
          length 128 (0-127)
            **************
    width   *            *
    64(0-63) *            *
            **************
  */

  /* Set the x y coordinates */
  u8g2.setCursor(32, 50);
  /* Set Font that Chinese or alphabet or icon  */
  /* find it https://github.com/olikraus/u8g2/wiki/fntgrptulamide */
  u8g2.setFont(u8g2_font_wqy13_t_gb2312);
  /* Output fonts in the font database which you set */
  u8g2.print("Hello world !");
  /*You can only display it if you send it.*/
  u8g2.sendBuffer();

  delay(1000);
 
  /* ======================for instance======================== */
  u8g2.clearBuffer(); //clear screen
  u8g2.setFont(u8g2_font_open_iconic_embedded_2x_t);
  u8g2.drawGlyph(111, 16, 64); //显示电池
  u8g2.drawGlyph(0, 16, 80);   //显示WIFI
  u8g2.setFont(u8g2_font_open_iconic_weather_4x_t);
  u8g2.drawGlyph(10, 50, 69);
  u8g2.setFont(u8g2_font_logisoso20_tf);
  u8g2.setCursor(48 + 3, 48);
  //float temp = bmp.readTemperature();
  float temp = 20.5;
  u8g2.printf("%.1f", temp);
  u8g2.print("°C");
  
  /*You can only display it if you send it.*/
  u8g2.sendBuffer();
  /* ======================for instance======================== */

  delay(1000);
}


```

Done.
