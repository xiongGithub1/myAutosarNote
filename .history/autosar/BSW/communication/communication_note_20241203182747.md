<!--
 * @Author: qinXiong
 * @Date: 2024-11-19 14:20:56
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-12-03 18:27:47
 * @Description: 
-->

# autosar 通讯模块笔记

## Can模块

### CAN物理层

![20241203180616](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203180616.png)

从图中可以看出，CAN总线通信需要CPU CAN控制器 can收发器参与。从CAN收发器引出两根线CANH CANL，所有节点都挂接到这两根线上，就形成了CAN的网络结构。图中有两路CAN总线，带有终端电阻120Ω 的是高速CAN，没有带终端120Ω的低速容错CAN。

#### **高速 CAN**

- **电路设计**
  高速CAN总线速率：一般是500KBps-1MBps
  高速CAN总线的总线设计如图所示，当前汽车领域用的最多的是CAN控制器集成到MCU中，使用外置CAN收发器，也就是图中右边这种设计。**(CAN收发器就是CAN的物理层，里面就是一些模拟电路比较复杂所以不集成到mcu中)**

![20241203181135](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203181135.png)

- **总线电压**

CAN总线信号由CANH CANL两根线的差分信号，也就是通过CANH和CANL的电压差来决定0 1信号。总线规定隐形电平为信号 1 显性电平为信号 0。其中隐形电平的时候CANH CANL都为2.5V，此时电压差就是OV，其中显性电平的时候CANH为3.5V CANL为1.5V，此时电压差就是2V。

![20241203181431](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203181431.png)

- **CAN线长度**

高速CAN，总线长度最大为40m，也就是当总线长度超过40m之后，总线的速率会受到影响。支线长度(节点和总线之间的距离)最长为0.3米。节点距离长度最大也是 40m。

![20241203181620](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203181620.png)

高速CAN线需要在CANH CANL加终端电阻，终端匹配电阻一般为120Ω。

![20241203181745](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203181745.png)

- **CAN线故障容错特性**

![20241203182145](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203182145.png)

对于高速CAN只有在CANL对GND短路时可以正常通信，其它协议中列举的故障下都不能正常通信。一般对于高速CAN关注的是当故障移除时，CAN控制器能否在规定的时间内恢复正常通信。

#### ***低速容错CAN***

- 电路设计

低速容错CAN总线速率:一般是40KBps-125KBps
低速容错CAN总线的总线设计和高速CAN一样，使用的最多的也是CAN控制器集成到MCU 中，使用外置CAN收发器。

![20241203182740](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203182740.png)


## spi模块

### 1.spi 概述

 SPI(SerialPeripheral Interface)总线是由摩托罗拉(Motorola)公司开发的**全双工同步串行总线（同步代表有时钟线）**，是微处理控制单元(MCU)和外围设备之间进行通信的同步串行端口。主要应用在EEPROMW、Fash、ASIC(专用集成芯片)等。通信速率一般能达到几M到几十M的速率，速率比I2C要快很多。
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

BSW层服务，中spi是通过复杂驱动层(CDD)来实现的,没有协议栈。**大概就是软件对底层做了一次封装，把SPI的硬件接口封装成软件接口，方便用户使用。**
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

Sequence:一个Job可以同时属于多个Seguence，图中Job0属于Seq0也属于Seq1。一个Sequence至少有一个Job,所以Seq2是不允许的。同一个Sequence下面的多个Job，他们拥有相同的优先级。
传输是以Scquence 为单位，**用户只能操作Sequence**，接收是具体到某个Channel。获取状态或者回调。Job及Sequence都可以(Level1，Leve12)
![20241119151412](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119151412.png)
![20241119145920](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119145920.png)
![20241119143119](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119143119.png)

- 一个Job 代表了一次完整的SPI通信**意思是从一次发到完整的接收**，一个Job可以由一个或者多个Channel组成**channel就是配置每一段是来干嘛的，比如第一段是读指令第二段就是发送寄存器地址,第三段就是接收数据**，Channel用来装载SPI的发送与接收数据。
- 一个Sequence可以由一个或者多个Job,可以将一个操作序列抽象成一个Sequence**将几次完整的通讯加到一个数组里面**.(当csn片选从设备是一个job操作时，第二个从设备也有一个job操作，所以需要一个Sequence来组合)。
- SPI传输的最小单元是由连续的Job组成的Sequence，依据Job的优先级将 Job 依次发送出去。

AUTOSAR为SPI提供了两种方式通信的接口。

1. **同步调用 Spi SyncTransmit** *同步函数被调用了就会一直把报文发出去*
2. **异步调用 Spi AsyncTransmit** *就不用一直发送报文，当发送完一个包就回调一个函数，用户自己决定何时发送下一个包*
   AUTOSAR中SPI驱动文件:
   Spi.c/Spi.h:SPI驱动文件，实现SPI收发函数
   Spi_PBcfg.c/Spi_PBcfg.h:Autosar配置生成文件配置具体Seq Job Ch
   Spi_Cfg.h:Autosar配置生成文件定义了一些宏
   ***最后:实际项目中大部分Seq Job Ch都是1:1:1，一般都是1路Spi挂载一路从机设备，操作一个Seq数据发送 会操作对应的 Job 和Ch。***

---

## 协议数据单元(PDU)和服务数据单元(SDU)

大家在 AutoSAR开发过程中，特别是通信和诊断开发中，应该会经常碰到两个概念PDU和SDU，可能对这两个概念不是很理解，这期我单独拎出来解释一下这两个概念。
**PDU(Protocol Data Unit)**在通信领域，协议数据单元PDU有以下几层含义:

- 网络的**对等实体**传送的信息单元，包括了**控制信息，地址信息或者数据**
- 在通信协议系统，在**指定的协议层传送的数据单元**，包含了该层的协议控制信息和用户信息。
  **SDU(Service DataUnit)**:服务数据单元，又叫**业务数据单元**，是指定层的用户服务数据，传送到接收方的时候同一协议层时数据没有发生变化，即业务部分。

在发给下层之后，下层将其**封装**在PDU中发送出去。服务数据单元是从高层协议来的信息单元传送到低层协议，**第N层的服务数据单元 SDU和上一层的协议数据单元PDU 是一一对应的。**
**PDU 和 SDU 的区别:**
**PDU 的封装/解封装**:在发送方，将用户递交的SDU加上协议控制信息PCI，封装成PDU;在接受方，收到PDU解封装，去掉PCI，还原成SDU交送接收方用户。

**SDU分段/装配**:如果下层通道的带宽不能满足单传递SDU的需求，就需要将一个SDU分成多段，分别封装成PDU发送出去(分段);在接收方再将这些PDU 解封装重新装配成 SDU。

**AutoSAR中的诊断传输层多帧传输主要就是使用该种方式。**(应用：CANTP，LinTP)

**SDU拼接/分离**:拼接是指发送方(n)层协议实体把多个长度较短的(n)SDU封装成一个(n)PDU来发送，在接受方再将收到的(n)PDU解封装，将多个(n)SDU分离出来。采用拼接功能的目的是提高通道利用率。

> 讲解用图
> ![20241119160656](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119160656.png)
> PDU:就是在TCP/IP协议中每一层就有自己的协议，PDU就是指每一层的协议，比如IP层就是IP协议，TCP层就是TCP协议，UDP层就是UDP协议，PDU就是指每一层的协议。
> SDU：是指业务数据，比如在TCP/IP协议中，TCP协议就是PDU，TCP协议里面有数据，比如HTTP协议，HTTP协议就是SDU，HTTP协议里面有数据，比如HTTP报文。**(上一层的pdu就是下一层的sdu)**

### autosar中的PDU和SDU应用

![20241119162232](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119162232.png)
还是这张图，图中显示了Eth、FlexRay、CAN、Lin通信协议栈。我们拿CAN协议栈来看:

    - CAN驱动:数据链路层该层传输的就是L-PDU(LinkPDU)
    - CANTP:网络传输层该层传输的就是N-PDU(NetWorkPU)
    - PduR:PduR路由层往上都属于应用交互层，传输的都是I-PDU(Interaction Pl)
其实对于CAN通信报文而言，I-PDU N-PDU L-PDU都是一样的，三种数据单元都是代表一帧CAN 报文。
对于CAN诊断而言，由于存在多帧传输，需要将N-SDU进行分段传输，分段成多帧N-PDU，N-PDU会加上N-PCI协议控制信息。![20241119162738](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119162738.png)

- **上层的PDU就是下层的SDU**
- PduR 传输N-SDU(也就是I-PDU)给到CanTp层，CanTp会通过分段多帧传输方式P将N-SDU分段成**多帧N-PDU**，在**每一帧N-PDU前加上N-PCI控制信息。(单帧也会加上N-PCI控制信息)**
- 每一帧 N-PDU就是数据链路层的L-SDU，数据链路层再加上CAN总线传输的ID等信息组成L-PDU，然后把L-PDU发送出去(**即CAN报文发送**)。

### 总结

PDU就是每一层的协议数据，SDU就是用户传输数据。对于CAN总线而言，只有在 CanTp这一层需要加上该层的协议数据单元N-PCI，从而实现多帧传输协议。

---

## COM模块

### COM 模块概述

AutoSAR Cp最核心的概念之一就是**面向信号**，也就是应用层不用关心底层具体是哪一条报文或者哪一种通信总线，只需要**使用基于信号接口**来获取信号值或者发送信号值，而**COM模块**主要功能就是给上层RTE提供**基于信号的接口**。
![20241119211601](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119211601.png)
***com模块就是将pdu模块的信号解析出来提供给RTE使用。***

**报文接收:**
Can、Lin、Eth等通信报文都会走到COM模块，COM 模块进行报文->信号的拆解，向上层 RTE 提供基于信号的接口。

**报文发送:**
RTE调用COM提供的基于信号的接口，COM模块进行信号->报文的组合，然后通过具体总线发送出去。
举个例子:
![20241119212654](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119212654.png)
这是一个DBC文件(**也就是CAN总线数据库文件，定义了详细的CAN报文和信号**)里面定义了message和signal，message就是一条can报文，signal就是报文里面的信号，而com 层向上层 RTE 提供的就是**基于信号**的接口。
![20241119212928](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119212928.png)
这张图是标准的**Rte和Com层交互接口**，应用层SWC只需要调用RTE具体信号的读写接口，就能接收和发送具体的信号值了，而**Rte就是调用了Com的Com SendSignal和Com ReceiveSignal 接。**
AutoSAR CP是面向信号的架构，也就是应用层SWC开发的时候都是直接通过信号来mapping，而真正实现信号 和报文分解和重组的是COM模块。COM模块作为通用模块，CAN、LIN、Eth的报文都可以交给COM层处理。

### COM内部功能介紹

首先还是要强调一下signal和message 的概念，以 CAN为例子:
**signal**:信号的概念

**message**:一帧CAN报文，8byte数据度，包含多个信号值，can 物理层发送message:的最小单元是message，也就是can总线一次发送就是一报文，也可以了理解成 I-PDU。

**想想看**:当RTE调用ComSendSignal之后，该信号所在的CAN报文是否马上发送??
答案N0，这要看信号属性和报文属性。

**信号的发送**:
![20241119214947](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241119214947.png)

上层RTE通过COM提供的ComSendSignal请求发送信号。此时将信号填充到PDU 中。

Com 内部有周期性函数Com MainFunctionTx,根据信号属性和PDU属性，确定发送逻辑。

Com会在信号更新后，根据信号的传输属性，来确定是否是直接传输。如果是直接传输，会设置标志位，在ComMainFunctionTx进行评估，是否发送该条报文，
I-PDU(报文)传输模式：
![1732024340817](image/communication_note/1732024340817.png)

**DIRECT**：调用了ComSendSignal，周期性ComMainFunctionTx执行了才发送。
**PERIODIC**：不管调没调用ComSendSignal，周期性ComMainFunctionTx执行了都会发送，没填入信号也会发送默认值。
**MIXED**:没有调用ComSendSignal，会按照周期性ComMainFunctionTx，有调用ComSendSignal，标志位更新，下一个周期性ComMainFunctionTx执行，根据标志位判断是否更新要发送。

**信号的接收**:
![20241120090037](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120090037.png)

- PduR 调用Com RxIndication 将I-PDU传给 Com
- Com将Pdu转换成信号
- Rte 通过 Com 提供的 Com_ReceiveSignal来接收信号
  Rte调用Com_ReceiveSignal有两种方式:
- 在Com 接收到信号的时候，通过调用Rte回调函数的方式。该种方式该信号值肯定是最新更新的信号值
- Rte层周期性调用ComReceiveSignal，不管此时该信号是否更新，可能会读到信号的旧值。这种方式也是可取的，将周期设置成5ms，是完全满足要求的。
  **信号组的概念**：
  信号组就是好几条同一条报文组成一个组，Rte通过调用以下函数时：

  - Com ReceiveSignalGroup
  - Com SendSignalGroup

实现发送信号组和接收信号组的效果。
实际上信号组就是提供了一个影子缓存区，将多个信号同步更新到I-PDU，起到一起发送或者接收的效果。

**信号网关**:
比如COM接收到CAN1的一帧报文，提取其中的网关信号，将该信号赋值到CAN2的指定信号，并通过CAN2发送出去，起到信号网关的作用。(**signal通过信号网关发出去了Rte也是能够读取的**)
![20241120091100](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120091100.png)

### 信号发送接收数据流

1. COM和PduR接口
   ![20241120092335](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120092335.png)
   ![1732065879167](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/1732065879167.png)
   **Lin模块有一个触发发送**
2. Rte、Com、PduR 三个模块发送确认时序图
   ![20241120092749](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120092749.png)
3. Rte、Com、PduR 三个模块接收通知时序图
   ![20241120093200](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120093200.png)
   ![20241120093315](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120093315.png)

### 总结

当COM模块是utoSAR通信中非常重要的模块，核心是实现了基于信号的接口，使得应用层可以面向信号开发。
COM 内部还实现了很多功能，比如:

- 字节序转换
- 超时监控(报文超时监控)
- 非法值处理等

更细的功能大家需要指代码来理解，基本上大家要对Com模块的基本功能有定的了解。
