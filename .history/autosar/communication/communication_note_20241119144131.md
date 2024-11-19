<!--
 * @Author: qinXiong
 * @Date: 2024-11-19 14:20:56
 * @LastEditors: xiongGithub1&&qx20001119@163.com
 * @LastEditTime: 2024-11-19 14:41:31
 * @Description: 
-->

# autosar 通讯模块笔记

## spi模块

### 1.spi 概述

 SPI(SerialPeripheral Interface)总线是由摩托罗拉(Motorola)公司开发的**全双工同步串行总线（同步代表有时钟线）**，是微处理控制单元(MCU)和外围设备之间进行通信的同步串行端口。主要应用在_EEPROMW、Fash、ASIC(专用集成芯片)等。通信速率一般能达到几M到几十M的速率，速率比I2C要快很多。
 SPI的通信原理是以主从方式工作，这种模式通常有一个主设备和一个或多个从设备，一般MCU是master。一般都是4根线，分别是SDI(数据输入)、SDO(数据输出)、SCLK(时钟)、CS(片选)。

* SDO/MOSI-主设备数据输出，从设备数据输入:
* SDI/MIS0-主设备数据输入，从设备数据输出:
* SCLK-时钟信号，由主设备产生:
* CS/SS-从设备使能信号，由主设备控制。当有多个从设备的时候，因为每个从设备上都有一个片选引脚接入到主设备机中，当我们的主设备和某个从设备通信时将需要将从设备对应的片选引脚电平拉低或者是拉高。
  ![20241119143119](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119143119.png)

### 2.spi 的四种模式

可以通过CPOL(时钟极性)和CPHA(时钟相位)来配置主设备的SPI模式。分别有如下4种通信模式:
1. 模式0(CPOL=0，CPHA=0)
模式0的特征:
CPOL=0:空闲时是低电平，第1个跳变沿是上升沿，第2个跳变沿是下降沿
CPHA=0:数据在第1个跳变沿(上升沿)采样
![20241119143602](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119143602.png)
2. 模式1(CPOL=0，CPHA=1)
CPOL=0:空闲时是低电平，第1个跳变沿是上升沿，第2个跳变沿是下降沿
CPHA=1:数据在第2个跳变沿(下降沿)采样
![20241119144013](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119144013.png)
3. 模式2(CPOL=1，CPHA=0)
模式2的特征:
CPOL=1:空闲时是高电平，第1个跳变沿是下降沿，第2个跳变沿是上升沿
CPHA=0:数据在第1个跳变沿(下降沿)采样
![1731998482267](image/communication_note/1731998482267.png)
4. 模式3(CPOL=1，CPHA=1)
