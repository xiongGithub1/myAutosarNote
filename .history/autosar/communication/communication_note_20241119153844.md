<!--
 * @Author: qinXiong
 * @Date: 2024-11-19 14:20:56
 * @LastEditors: xiongGithub1&&qx20001119@163.com
 * @LastEditTime: 2024-11-19 15:38:44
 * @Description: 
-->

# autosar 通讯模块笔记

## spi模块

### 1.spi 概述

 SPI(SerialPeripheral Interface)总线是由摩托罗拉(Motorola)公司开发的**全双工同步串行总线（同步代表有时钟线）**，是微处理控制单元(MCU)和外围设备之间进行通信的同步串行端口。主要应用在_EEPROMW、Fash、ASIC(专用集成芯片)等。通信速率一般能达到几M到几十M的速率，速率比I2C要快很多。
 SPI的通信原理是以主从方式工作，这种模式通常有一个主设备和一个或多个从设备，一般MCU是master。一般都是4根线，分别是SDI(数据输入)、SDO(数据输出)、SCLK(时钟)、CS(片选)。

* SDO/MOSI-主设备数据输出，从设备数据输入:
* SDI/MISO-主设备数据输入，从设备数据输出:
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
   模式3的特征:
   CPOL=1:空闲时是高电平，第1个跳变沿是下降沿，第2个跳变沿是上升沿
   CPHA=1:数据在第2个跳变沿(上升沿)采样
   ![1731998585103](image/communication_note/1731998585103.png)
   下图是SPI主设备读取从设备寄存器数据的时序图，CS默认是高电平，SPI把CS拉低开始通信。先发送8bit的读命令，再发送24bit的从设备寄存器地址，接着从设备会发送寄存器数据给主设备，完成一次完整的读通信。
   (下图DIDO是针对从设备说的DI就是主设备发从设备收D0就是主设备收从设备发)
   **注意:SPI是全双工通信，当主设备在DI上发送数据的时候，D0上的信号是高阻态，主设备会屏蔽 D0 上的数据。当从设备在 D0 上发数据的时候，D1上主设备也会发送默认数据，一般是 0xFF。**
   ![20241119145920](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119145920.png)

### 3.autosar spi

BSW层服务，中spi是通过复杂驱动层(CDD)来实现的,没有协议栈。
![20241119150747](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119150747.png)
在Autosar中，由于要考虑通信的安全以及软件的可扩展性，所以不能像编写单片机 SPI驱动那样写一个简单的收发函数。Autosar中定义了很多软件层面的数据结构以及API接口，其中最关键的是Sequence，Job，Channel三种软件层面的数据结构。下图是罗列了一些重要概念和关键点:
![20241119150928](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119150928.png)
**Autosar SPI(仅支持Master模式)仅支持全双工模式
图中的Level2级别的APT是为那些需要单独提供至少两路SPI总线的芯片指定。**

1. Sequence/Job/Channel:
![1732001476795](image/communication_note/1732001476795.png)

首先需要明确，这三个概念都是软件层面的，不是物理的SPI通道的意思。

Job:这个概念是和具体SPI外设绑定的，也就是每一个Job只绑定一个SPI从机设备。但是多个Job可以绑定同一个SPI从机设备**就是不存在job1属于Dev1和Dev2这种情况**。一个Job下面可以有多个Channel，如上Jobl，Job2，Job3:且应至少有一个Channel，否则没意义，如上Job5是不允许的。这种情况通常是这个Job/Seq有多个用户使用，针对每个用户都分配一个自己独立的Channel。

Channel;Channel主要是对BUFF做了一些配置，一个Channel可以同时属于多个Job，上面的CH2 和CH5 就是这样，CH2 既属于Jobl也属于Job2，CH5既属于Job3又属于Job4.CH2可以用于Devl和 Dev2,CH5只用于Dev2.

Sequence:一个Job可以同时属于多个Seguence，图中Job0属于Seg0也属于Segl。一个Sequence至少有一个Job:所以Seq2是不允许的。同一个Sequence下面的多个Job，他们拥有相同的优先级。
传输是以Scquence 为单位，只能操作Sequence，接收是具体到某个Channel。获取状态线者回调。Job及Sequence可以(Leel1，Leve12)
![20241119151412](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119151412.png)
![20241119145920](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119145920.png)
![20241119143119](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119143119.png)

- 一个Job 代表了一次完整的SPI通信**意思是从一次发到完整的接收**，一个Job可以由一个或者多个Channel组成**channel就是配置每一段是来干嘛的，比如第一段是读指令第二段就是发送寄存器地址,第三段就是接收数据**，Channel用来装载SPI的发送与接收数据。
- 一个Sequence可以由一个或者多个Job,可以将一个操作序列抽象成一个Sequence**将几次完整的通讯加到一个数组里面**.(当csn片选从设备是一个job操作时，第二个从设备也有一个job操作，所以需要一个Sequence来组合)。
- SPI传输的最小单元是由连续的Job组成的Sequence，依据Job的优先级将 Job 依次发送出去。
