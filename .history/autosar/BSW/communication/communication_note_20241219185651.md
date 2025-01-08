<!--
 * @Author: qinXiong
 * @Date: 2024-11-19 14:20:56
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-12-19 18:56:51
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

- **总线电压**

低速容错CAN总线信号也是由CANH CANL两根线的差分信号，也就是通过CANH和CANL的电压差来决定0 1信号。总线规定隐形电平为信号1 显性电平为信号0。其中隐形电平的时候CANH为OV CANL都为5V，此时电压差就是-5V，显性电平的时候CANH为3.5V CANL为1.5V，此时电压差就是 2V。

- **CAN线拓扑结构**
  低速容错CAN除了支持总线型还支持星型。

![20241203183026](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203183026.png)

- **CAN线故障容错特性**

对于低速容错CAN，只有在CANH和CANL同时短路或者同时开路时不能正常通信，其它故障下都能够正常通信。这是由于低速容错CAN支持单线模式，当检测到一条线故障时，可以进入单线模式进行通信，此时会选定一个值作为参考电压，例如2.5V。在单线模式时，低速CAN会继续监视另外一条线，当故障排除后改用双线模式。所以称之为低速容错CAN。

### CAN数据链路层

#### 1.can 总线标准和帧格式

最新的CAN总线数据链路层协议为CAN2.0BActive，支持标准和扩展帧。

![20241203183907](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203183907.png)

底层协议和IS0标准以及对应硬件的关系如图：

![20241203184004](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203184004.png)

CAN总线收发器属于CAN的物理层，主要实现的就是CAN总线上面标准的电平信号。CAN控制器属于CAN的物理链路层，则实现了对CAN电平信号转成CAN报文的解析。

CAN总线有以下几种帧格式:

- 数据帧： 携带从发送节点到接收节点的数据

![20241203184152](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203184152.png)

- 远程帧： 向其他节点请求发送具有同一标识符的数据帧**(A发送B请求数据，B发回数据，A发送给B的就是远程帧)**

![20241203184832](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203184832.png)
![20241203184923](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203184923.png)

- 错误帧： 携带错误信息，比如发送数据帧时，发送方没有收到接收方反馈，则发送方发送错误帧。

![20241203185028](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203185028.png)

- 超载帧:在先行的和后续的数据帧(或者远程帧)之前附加一段延时，用于接收单元通知其未做好接收准备的帧

![20241203185352](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203185352.png)

- 帧间隙:用于将数据帧或者远程帧与前面的帧分开

![20241203185445](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203185445.png)

#### 2.can 通信机制和数据帧

**A.通讯机制**
CAN 总线数据链路层实现了如下的CAN 通信机制:

- 多主的基于优先级的总线访问
- 非破坏性的基于竞争的仲裁
- 远程数据请求
- 配置灵活性
- 错误检测
- 报文自动重发
- 临时与永久错误界定

**仲裁机制**

- **退出仲裁后进入“只听状态**
- **在总线空闲是进行报文重发**

可以通过CAN ID号进行报文仲裁，CAN ID号越小，报文优先级越高。实现该机制是由于总线支持**“线与“机制**，也就是**显性位可以覆盖隐形位(0覆盖1)**。

![20241203185858](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203185858.png)

A节点ID为75=  000  0100 1011b
B节点ID为250= 000  1111 1010b
C节点ID为1000=011  1110 1000b

![20241203190023](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203190023.png)

在ABC三个节点ID仲裁阶段，C节点先退出仲裁，进入只听模式。然后B节点退出仲裁进入只听模式。所以A节点会先发报文，发完报文总线空闲的时候，B节点和C节点进行仲裁，B节点报文ID小于C节点报文ID，B会先发，C节点会最后发。**注:标准帧优先级比扩展帧高**

**报文过滤机制**:通过过滤器对接收的报文进行过滤
如果相关->接收;如果不相关->过滤

![20241203190816](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203190816.png)
**(屏蔽寄存器里面的是1的才关注只有满足报文缓冲区的值才接收。)**

**B.数据帧**

![20241203191326](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191326.png)
SOF：帧起始

- 标识一个数据帧的开始，**用于同步**
- 一个**显性**位
- 只有在总线**空闲期间**节点才能够**发送SOF**
  ID:唯一确定一条报文
- 表明报文的含义，可以包含报文的源地址和目标地址
- 确定报文的仲裁优先级，ID数值越小，优先级越高
- 标准帧11位，扩展帧29位

![20241203191633](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191633.png)

RTR:用于区别数据帧和远程帧

- 数据帧，RTR=0
- 远程帧，RTR=1
- 当RTR=1为远程帧的时候，是不需要数据场的，远程帧的作用就是请求其他模块发包。

![20241203191742](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191742.png)

IDE位:用于区别标准帧和扩展帧
标准帧，IDE=0(11位 ID)
扩展帧，IDE=1(29位 ID)

![20241203191848](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191848.png)

![20241203191907](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191907.png)

SRR位:只有扩展帧有SRR，表明在该位替代了标准帧中的RTR位

- 该位无实际意义
- SRR 永远置1

![20241203191956](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203191956.png)

r0、r1位:

- 两个保留位
- 当前置 0

![20241203192127](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192127.png)

DLC 位:

- 包含4位，表示数据场包含数据的字节数，CAN报文数据场最长为8字节
- DLC = 0-8
- DLC =9-15->DLC=8

![20241203192213](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192213.png)

Data Field 数据场:

- 具有0-8个字节长度，由DLC确定长度
- 包含CAN 数据帧发送的内容
  ![20241203192334](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192334.png)

CRC:
用于进行 CRC 校验

![20241203192404](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192404.png)
![20241203192452](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192452.png)

DEL:CRC界定符

- 界定CRC列固定格式，1个隐形位
- CRC界定符之前进行位填充
  ![20241203192557](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192557.png)

ACK:
用来确定报文被至少一个节点正确接收**(A发送数据到ack位了之后，开始读总线上的数据，看是否有一个节点接收到，B接收到数据之后拉高总线电平)

![20241203192638](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203192638.png)

EOF:

- 表示数据帧接收
- 固定格式，7个连续的隐形位

ITM:

- 固定格式，3个连续的隐形位
- ITM之后进入总线空闲状态，此时节点可以发送报文

![20241203193043](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241203193043.png)

#### 3.can 错误检测和错误帧

**节点发送报文时要检测总线状态**

- 只有总线空闲，节点才能发送报文。
- 在发送报文过程中进行“回读”，判断送出的位与回读的位是否一致。

![20241204183018](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204183018.png)

错误检测类型：

![20241204183051](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204183051.png)

**1、位检测->位错误**

- 检测到总线位状态与自身送出的位不同
- 仲裁或者ACK位期间送出“隐性”位除外
- 位检测范围一直到EOF结束

**2、填充检测->填充错误**

- 发送节点进行位填充，位填充编码是发送5个连续相同的极性位后，自动插入一个极性相反的位。
- 接收节点清除位填充。
- 检测到违背位填充规则，也就是接收到6个连续相同极性位。
- 填充检测范围是在CRC鉴定符DEL之前

**3、CRC检测->CRC错误**

- 节点计算的CRC序列与接收到的CRC列不同

**4、格式检测->格式错误**

固定格式位场(如CRC界定符、ACK界定符、结束等)含有一个或者更多非法位。

**5、ACK检测->ACK错误**

发送节点在ACK位期间未检测到“显性”位

**发送节点会检测到**:位错误、格式错误、ACK错误
**接收节点会检测到**:填充错误、格式错误、CRC错误

错误界定:
节点通过REC(接收错误计数器)和TEC(发送错误计数器)值，来实现节点错误状态的转换。

- 当接收错误产生时REC增加，正确接收到数据帧时，REC减少
- 在发送错误产生时TEC增加，正确发送数据帧，TEC减少
 
根据REC和TEC的值，主要有三种状态:主动错误、被动错误、Bus Off .

![20241204184552](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204184552.png)

主动错误和被动错误的区别在于:

- 主动错误:检测到错误，立即主动发送错误标志，6个连续的**显性位**(会破坏掉总线上传输的报文)
- 被动错误:检测到错误，只能“被动地”等其他的节点报错，发送6个连续**隐性位**，(隐形位可能被其他节点的显性位覆盖)，诱发接收节点发送错误标志，直到识别出其他节点主动报错。**(如果一直发错误的6个隐形就证明可能就是自己当前节点有问题)**

错误帧格式:
1.包括错误标志和错误界定符
2.错误标志:主动错误6个显性位 被动错误6个隐性位

主动错误帧发送:

- 位错误、填充错误、格式错误或者 ACK 错误产生后->错误标志在下一位发
送
- CRC 错误->错误标志在 ACK 界定符后发送
- 错误帧发送后:总线空闲时重发出错的数据帧

![20241204185950](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204185950.png)

![20241204191701](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204191701.png)
**(发送节点和接收节点一起往总线上发送错误标志)**

![20241204191911](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241204191911.png)

#### 4.位定时和同步

**一、位时间**
由于CAN属于异步通讯，没有**时钟信号线**，同一网络中的所有节点像串口异步通讯一样，节点间会使用约定好的波特率进行通讯。CAN还会使用“位同步”来对抗干扰、吸收误差。
位时间指的是CAN总线上1bit 数据的时间，一个位时间包括:

![20241217181707](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217181707.png)

一个位时间包含4个时间段，8-25个时间份额Time Quantum
![20241217181914](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217181914.png)
![20241217181945](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217181945.png)

**同步段SS(SYNCSEG)**:用于同步网络中各个节点，跳变沿如果发生在同步段，说明节点和通讯时序是同步的，固定1个Time quantum。
**传播段PTS(PROPSEG)**:用于补偿网络的物理延时时间，1-8个Time
quantum
**相位缓冲段1PES1(PHASESEG1)**:用于补偿节点间的晶振误差，允许通过***重同步**对该段加长，在这个时间段末端进行总线状态的采样(1-8tq)
**相位缓冲段 2PES2(PHASE SEG2)**:用于补偿节点间的晶振误差，允许通过**重同步**对该段缩短(1-8tq)

**二、同步**
can 的同步包括**硬同步**和**重同步**两种方式
同步规则:
- 一个位时间内只允许一种同步方式
- 任何一个“隐性“到“显性”的跳变都可用于同步
- **“硬同步”**发生在SOF，所有接收节点调整各自当前位的同步段，使其位于发送的 SOF 位内。只有在帧起始信号SOF时有用，无法确保后续位时序是否同步。
- “重同步“发生在一个帧的其他位场内，当跳变沿落在同步段之外。
- 在 SOF 到仲裁场有多个节点同时发送的情况下，”发送节点对跳变沿不进行重同步。
**硬同步:**
也就是一帧CAN报文开始的SOF位的时候，总线上接收节点会进行一次硬同步，让所有接收节点调整各自当前位的同步段，调整的宽度有限。一帧数据后面位置产生相位偏移的时候，就需要使用重同步来重新同步，某节点检测到总线的帧起始信号不在节点内部时序的SS段范围，会判断自己内部时序和总线不同步，该节点通过硬同步方式重新调整，把自己的SS段移到总线下降沿的部分，从而获得同步。
![20241217183109](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217183109.png)
**重同步**
当跳变沿和同步段误差小于 SJW(reSynchronization Jump Width;重新同步补偿宽度)，重新同步会通过延长 PTS1段或者缩短PTS2 段，来保证采样点位置的正确;如限定 SJW=5 Tq 时，单词同步调整的时候不能增加或者减少超过 5Tq 的时间长度，若有需要，控制器会通过多次小幅度调整来实现同步)
![20241217183446](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217183446.png)

位定时:
传播段延时时间确定
![20241217183643](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217183643.png)

位定时参数确定:
![20241217183720](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217183720.png)

**总 结**
CAN 总线作为车载通信最重要的总线,与其很好抗干扰性、错误检测机制、不需要时钟线等机制都密切相关。学好 CAN 总线不仅仅要学好CAN 的网络协议栈，对其底层通讯技术最好也要有所理解，这样才能明白CAN 总线的本质。
#### 5.总结

### autosar通信服务架构
#### 1.AutoSAR通信服务架构
![20241217185206](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217185206.png)

我们在之前讲了BSW基础软件层的基本服务，这章针对通信和诊断服务我们具体讲一下(本讲主要针对通信服务，诊断服务放到专门的章节描述)。从图中可以看出，AutoSAR中的通信服务分层还是非常清晰的:
- **Mcal**包含了收发器驱动和总线控制器驱动，Mcal向上提供驱动接口供总线接口层(CanIf LinIf EthIf)调用。
- **总线接口层**(CanIf LinIf EthIf)也就是通信硬件抽象层，主要任务包括向上层模块提供与硬件无关的统一接口，屏蔽下层控制器收发器实现细节。
- **Bus Tp层**:Tp(Transport Layer)是通信传输层，主要是为诊断使用的，当can Lin总线需要传输大于8byte 数据，就需要Tp层进行多帧传输。
- **PduR层**：Pdu Router也就是Pdu路由层，所有的通信收发都会到这一层进行PDU路由。Autosar中包含了Can、Lin、Eth等通信，每个通信报文都可以描述成一个 PDU(protocal data unit)协议数据单元，通过 PduR 这一层统一管理每个 Pdu 收发去处。
- **IPDU MuX**:IPDU多路复用功能，指的是使用同一个I-PDU的同一种PCI，其SDU有多个不同的布局。后续会在PduR模块详细描述。
- **COM**:通信报文会到这里。**从PDUR接收上来的I-PDU到这里会转成具体信号数据给到应用层使用**，应用层通过 RTE传下来的信号首先到这里转成 I-PDU发到 PduR。应用层无需关注收发数据是通过什么总线传输的，这些收发的数据通过 DBC文件或者ARXML,文件事先定义好。COM主要起到信号接口和网关作用。后续会在 COM 模块详细描述。
- **DCM**:诊断报文会到这里，根据诊断要求做具体诊断服务。后续诊断详细
讲解。
### 2.协议数据单元PDU
![20241217190446](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217190446.png)

这个图是 AutoSAR 官方文档中截取，这个图把 Autosar 的通信服务很细致的面描述出来了。包括Eth、FlexRay、CAN、LIN 4种总线通信协议栈。**L-PDU**:Data Link Layer PDU 数据链路层 PDU，可以理解就是一帧总线报文 **N-PDU**:NetworkLayerPDU网络层(也就是传输TP层)PDU，一般诊断报文会走TP层，通信报文直接从 IF层转到PduR层，当诊断是多帧传输的时候，一个I-PDU就会被分段成多个N-PDU
**I-PDU**:Interaction Layer PDU交互层PDU，PDUR路由转发I-PDU。三种PDU代表在通信协议栈不同分层的协议数据单元，I-PDU就包含了数据buffer 指针、数据长度、和 I-PDU ID，本质就是一个结构体。
### 3.通信服务传输数据流
**发送流程**：
![20241217191413](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217191413.png)

1.应用层模块通过RET调用C0M模块ComSendSignal请求发送信号(Id,Value)
2.COM写信号进PDUbuffer
3.PDU被事先定义好的PDU路由表，发送到指定目的层(根据PDU ID来查找PDU的路由表)，比如CAN总线的PDU就会路由到CanIf层，Lin的PDU就会路由到LinIf 层。
4.Interface 层根据不同的通道，把报文写入到不同队列中
5.Driver根据报文优先级发送报文
**注意:这里涉及到PDU的buffer缓存问题，一般情况下上层到If层都不会有 PDU拷贝过程，直接把 Buffer 缓存指针进行传递。到了驱动层可能会拷贝进入驱动buffer进行发送。这样可以提高传输效率，节省RAM资源。**

**接收流程**：
![20241217191814](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241217191814.png)

1.驱动通过轮询或者终端接收报文
2.驱动调用IF层的RxIndication将数据传递给Interface层
3.Interface 调用 PduR 层的 **RxIndication 将数据传递给 PduR层**
4.PduR层根据PDU ID找到路由表，路由到指定上层，通信报文一般都路由到COM层。
5.进入COM层之后，根据SWCs的情况，要么直接通过RTE把信号给到SWCs，要么缓存到自己的Buffer。
**注意:这里也涉及到PDU的buffer缓存问题，一般情况从驱动上来也不会有Buffer拷贝，只有到COM层才会有自己的Buffer，这样可以提高传输效率，节省RAM资源。**
### 4.总结
这一章把Autosar通信服务的概述讲了一遍，大家要对PDU、数据收发链路有一定的了解，诊断服务放到诊断章节描述。这里很多没有铺开讲解，后续会在每一个具体模块中详细描述。

### CAN总线网络传输层

#### 1.Can诊断网络分层

Can 诊断的网络分层也是参考0SI模型，该模型定义了网络互联的7层架构(物理层、数据链路层、网络层、传输层、会话层、表示层和应用层)。

Can 诊断通信包含了诊断应用层(ISO 15765-3/ISO14229)、网络层(ISO 15765-2)、数据链路层(ISO 11898-1)和物理层。
![20241219185642](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241219185642.png)

当前随着统一诊断(UDS)服务发展，诊断应用层已经基本使用IS014229标准。我们今天CanTp模块就属于网络传输层，就是使用ISO 15765-2的标准。

**注意:这里可以把传输层和网络层放一起，都是由 CanTp模块实现。**


#### 2.CanTp模块基本功能介绍
#### 3.CanTp组帧和拆帧过程
#### 4.网络层时间参数要求
#### 5.诊断数据传输流程

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
