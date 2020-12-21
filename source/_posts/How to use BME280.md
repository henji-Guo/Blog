---
title: How to use BME280
author: henji
img: 
top: false
cover: false
coverImg: 
password: 
toc: true
mathjax: false
summary: Using BME280 to read
categories: ESP32
tags:
  - ESP32
  - Arduino
date: 2020-11-15 00:07:36
---

##  BME280 Library
### 1. Open Arduino IDE
![](https://img-blog.csdnimg.cn/20201114204123125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### 2. Select the board
![](https://img-blog.csdnimg.cn/20201114204223441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### 3. Select the board 
![](https://img-blog.csdnimg.cn/20201114204257892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### 4. click the Manage Libraries
![](https://img-blog.csdnimg.cn/20201114204403545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### 5. Installing the BME280 Library
![](https://img-blog.csdnimg.cn/20201114204914197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
![](https://img-blog.csdnimg.cn/20201114204953399.png#pic_center)
Done.

##  IIC Instance
### IIC PIN 
![](https://img-blog.csdnimg.cn/20201114220512251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### Code
 ```c
/*
   ==================================================================================

   SPDX-License-Identifier: GPL-3.0-or-later

   File_name : BME280_IIC.ino
   Describe  : Using IIC to read BME280
   Author    : GHJ
   Date      : 2020年11月14日 21:00:00

   PS:
      需要BME280->BME280库:https://github.com/adafruit/Adafruit_BME280_Library.git

   Change Logs:
   Date               Author          Notes
   2020年11月14日       GHJ         the first version

   ===================================================================================
*/

#include <Arduino.h>
#include <Adafruit_BME280.h>

/*       Interconnect
 *  
 *  ESP32  <--PIN-->   BME280
 *  G22    <------->   SCL
 *  G21    <------->   SDA 
 *  3.3V   <------->   VCC 
 *  GND    <------->   GND 
 *  
*/  

/*Slave address ,according to the BME280 Datasheet*/
#define IIC_address_BME280 0x76

/* Using IIC  */
/*The Wire is equivalent to IIC.*/
Adafruit_BME280 bme;

void setup()
{
  Serial.begin(115200);

  int count = 0;
  while (!bme.begin(IIC_address_BME280, &Wire))
  {
    if (count == 5)
    {
      Serial.println("It took too long to initialize , Pls check any about your sensor !!!");
      //ESP.restart(); //restart ESP32
    }
    Serial.println("The sensor of bme280 is Initializing ... ");
    delay(1000);
    count++;
  }
  Serial.println("The Sensor of BME280 initiated");
}

void loop()
{
  Serial.print("Temperature: ");
  Serial.print(bme.readTemperature());
  Serial.println(" °C");
  Serial.print("Humidity: ");
  Serial.print(bme.readHumidity());
  Serial.println(" RH%");
  Serial.print("Air pressure : ");
  Serial.print(bme.readPressure());
  Serial.println(" Pa");
  delay(1000);
}

```
##  SPI Instance
### SPI PIN
![](https://img-blog.csdnimg.cn/20201114222619627.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9oZW5qaS1ndW8uZ2l0aHViLmlv,size_16,color_bb2595,t_100#pic_center)
### code
```c
/*
   ==================================================================================

   SPDX-License-Identifier: GPL-3.0-or-later

   File_name : BME280_SPI.ino
   Describe  : Using SPI to read BME280
   Author    : GHJ
   Date      : 2020年11月14日 21:00:00

   PS:
      需要BME280->BME280库:https://github.com/adafruit/Adafruit_BME280_Library.git

   Change Logs:
   Date               Author          Notes
   2020年11月14日       GHJ         the first version

   ===================================================================================
*/

#include <Arduino.h>
#include <Adafruit_BME280.h>

/*       Interconnect
 *  
 *  ESP32  <--PIN-->   BME280
 *  3.3V   <------->   VCC 
 *  GND    <------->   GND
 *  SCLK   <------->   SCL 
 *  MOSI   <------->   SDA
 *  MISO   <------->   SDO
 *  SS     <------->   CSB
 */  
/*SPI Chip Selected . Low voltage active*/
#define SCLK 5

/* Using SPI  */
/*The SPI .*/
Adafruit_BME280 bme(SCLK,&SPI);

void setup()
{
  Serial.begin(115200);

  int count = 0;
  while (!bme.begin())
  {
    if (count == 5)
    {
      Serial.println("It took too long to initialize , Pls check any about your sensor !!!");
      ESP.restart(); //restart ESP32
    }
    Serial.println("The sensor of bme280 is Initializing ... ");
    delay(1000);
    count++;
  }
  Serial.println("The Sensor of BME280 initiated");
}

void loop()
{
  Serial.print("Temperature: ");
  Serial.print(bme.readTemperature());
  Serial.println(" °C");
  Serial.print("Humidity: ");
  Serial.print(bme.readHumidity());
  Serial.println(" RH%");
  Serial.print("Air pressure : ");
  Serial.print(bme.readPressure());
  Serial.println(" Pa");
  delay(1000);
}
```



Done .
