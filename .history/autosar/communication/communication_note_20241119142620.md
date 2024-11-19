<!--
 * @Author: qinXiong
 * @Date: 2024-11-19 14:20:56
 * @LastEditors: xiongGithub1&&qx20001119@163.com
 * @LastEditTime: 2024-11-19 14:25:31
 * @Description: 
-->
# autosar 通讯模块笔记

## spi模块
 ### spi 概述
    SPI(SerialPeripheral Interface)总线是由摩托罗拉(Motorola)公司开发的企双工同步串行总线，是微处理控制单元(MCU)和外围设备之间进行通信的同步串行端口。主要应用在_EEPROMW、Fash、ASIC(专用集成芯片)等。通信速率一般能达到几M到几十M的速率，速率比I2C要快很多。
    SPI的通信原理是以主从方式工作，这种模式通常有一个主设备和一个或多个从设备，一般MCU是master。一般都是4根线，分别是SDI(数据输入)、SDO(数据输出)、SCLK(时钟)、CS(片选)。