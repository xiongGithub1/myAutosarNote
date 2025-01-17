<!--
 * @Author: qinXiong
 * @Date: 2024-11-20 09:41:40
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-22 16:08:20
 * @Description: 
-->

# 诊断笔记

## 诊断基础知识

### 1. 什么是汽车诊断

> autosar框架图
> ![20241120094506](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120094506.png)

从事汽车电子开发的，基本都需要接触汽车诊断，那到底什么是汽车诊断呢?
![20241120094622](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120094622.png)

从这个图可以看出，诊断是工程师使用诊断工具和汽车电子ECU直接进行通话，常见的场景是当汽车出现故障时，4S店工作人员拿着诊断仪连接汽车，读取相关故障原因。

**其实像病人看病一样，医生拿着诊断仪器，病人相当于汽车ECU，医生问一句，病人答一句。**

汽车诊断通过汽车总线(Can Lin Eth)来交流，大部分通过CAN总线通信来进行诊断会话。

#### Can诊断网络分层

### 2. 诊断网络分层

诊断可以走CAN LIN ETH等总线，这里以CAN诊断为例讲解。

Can 诊断的网络分层也是参考0SI模型，该模型定义了网络互联的7层架构(物理层、数据链路层、网络层、传输层、会话层。表示层和应用层)。
Can 诊断通信包含了诊断应用层(IS015765-3IS014229)、网络层(IS015765-2)、数据链路层(IS011898-1)和物理层。

![20241120095609](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120095609.png)

当前随着统一诊断(UDS)服务发展，诊断应用层已经基本使用IS014229标准。我们今天 CanTp模块就属于网络传输层，就是使用IS015765-2的标准。

**注意:这里可以把传输层和网络层放一起，都是由 CanTp模块实现。**

DCM内部支持UDS服务和OBD服务。

**注:UDS和 OBD服务都属于诊断应用层服务，底层可以通过不同的总线通信实现。比如UDS服务支持CAN总线Lin总线Eth总线，0BD常见的支持CAN总线、K线、FlexRay 总线。大家一定要区分诊断服务并不规定一定要通过什么总线实现。**

### 3. AutoSAR诊断功能

![20241120100348](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120100348.png)

从图中可以看出，诊断服务属于AutoSAR服务层，诊断数据流通过CAN驱动CANIF层CANTP PDUR DCM给到DCM块，DCM模块处理诊断数据，并执行对应的具体诊断服务。

DCM:Diagnostic Communication Manager，诊断通信管理。实现具体的诊断协议，比始UDS OBD。这里具体定义了各种不同的诊断服务，比如读取ECU故障码、写入DID数据等。

DEM:Diagnostic Event Manager 诊断事件管理，用来记录和存储诊断事件的，在诊断故障码写入的时候会加入防抖策略。

FIM:Function Inhibition Manager，功能禁止管理。当一些错误出现的时候，禁止一些功能，比如检测到电流过大的时候，关闭继电器。就是有些SWC是需要使能条件的，这些条件取决于故障内容(源于DEM)，而FIM负责根据故障内容关闭一些SWC或执行一些SWC。

DCM和DEM直接交互，只要在0x19 0x14等与DTC相关服务的时候进行交互。

故障响应:应用层传输数据到 DEM，DEM判断出是哪个故障，之后发送给FIM;FIM立即做出判断，通过回调函数通知SWC或者轮询的方式禁止SWC

故障记录:应用层传输数据给DEM，DEM一边发送给FIM，另一边发送给NVM，NVM会将DTC存储到FLASH中。

### 4.总结

诊断作为汽车电子软件非常重要的功能特性，一定要学会。后续会将DCM DEM FIM等模块详细讲解，并详细讲解UDS服务和OBD服务。

## UDS基础知识

### 1. UDS服务分类

![20241120102947](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120102947.png)

UDS服务(Unified Diagnostic Service)统一诊断服务协议，IS014229对UDS 服务分成了几大类

- 诊断和通信管理功能组
- 数据传输功能组
- 存储数据传输功能组
- 输入输出控制功能组
- 例行程序功能组
- 上传下载功能组

其中诊断服务有**支持子服务**和**不支持子服务**之分。

### 2. 诊断服务数据格式

UDS展务是通过诊断请游和诊断响应组成的，一般情况下是诊断仪发出诊请求，ECU根据诊断请求批行具体诊斯服务，并将结果通过诊斯应答发出给诊仪。

诊断服务分为带子服务的请求和不带子服务的请求，响应分为正响应和负响应，正响应会区分对应带子服务的响应和不带子殿务的响应。负响应都是一样的。

![20241120135435](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120135435.png)

下面缩写字母解释

M:强制的 必须的
S:强制的，需从参数列表中选择一个
U:用户自定义。可选的
**注:下面数据都是16进制数，省去“0x”前缀**

- 带有子服务的请求报文

由服务ID，子服务ID，数据参数(可选)组成
![20241120140143](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120140143.png)

例如:10 01(请求切换到默认会话模式)**(10:就是服务id,01 就是子服务id，放在D1中)**
例如:19 02 FF(请求读取以0xFF为期sk的故障信息)**(19:就是服务id,02:就是子服务id,FF： 请求读取以0xFF为期sk的故障信息)**

- 不带子服务的请求报文

由服务ID。数据参数(可选)组成。
![20241120140839](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120140839.png)

例如:22 F0 90 (请求读取DID为0xF090的数)**(需要在ECU中定义的0xF090的DID才能发)**
例如:37 (请求数据传输退出服务)

- 含有子服务正响应报文

由响应ID，子服务1D，数据参数(可选)组成，其中响应ID值为对应请求ID+0x40

![20241120141041](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120141041.png)

例如:

请求:10 01(切换默认会话模式)
响应:50 01 xx xx xx xx(成功切换默认会话模式)**(50的来历:10+0x40)**

- 不含子服务正响应报文

由响应1D，数据参数(可选)组成。其中响应ID为对应请求ID+0x40
![20241120141539](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120141539.png)

例如:
请求:22 F0 90(读取DID为0xF090的数据)
响应:62 F0 90 11 11 11 11 11 11 11 11 11 11(返回DID为0xF0 90 的数据为11 11 11 11 11 11 11 11 11 11)

- 负响应报文

![20241120141811](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120141811.png)

例如:
请求:10 02(切换编程会话模式)
负响应:7F 10 7E(切换编程会话服务执行失败 错误码 SEC为7E)**(7F：负响应，10：请求服务id,7E: 错误码)**

### 3. 诊断寻址方式

CAN总线是广播形式的通信，即一条报文发送后，CAN网络中的所有节点都可以收到该报文，诊断仪在发送诊断请求报文后，具体是想跟网络中的哪个ECU进行诊断会话呢，这个是通过什么方式判断的?这貌引出了**寻址方式**的概念。

寻址方式有两种，**物理寻址**和**功能寻址**。

- 物理寻址

是诊断仪和单个ECU之间的诊断，也就是诊断请求报文发出去后，根据报文ID，CAN 网络中只会有对应的一个ECU进行诊断响应。**(1:1的方式)**

- 功能寻址

是诊断仪和多个ECU之间的诊断，也就是诊断请求报文发出去后，CAN网络中支持该功能寻址报文ID的ECU，一般功能寻址报文ID为**0x7DF**。这些ECU都会执行诊断服务，并且发出诊断响应。**(1:N的方式)**

* **一个EOU 内部一般会定义3条诊断报文**:

- 诊断请求接收报文(物理寻址报文 ID用户自定文 同一网络中的每个ECU不一样)
- 诊断请求接收报文(功能寻址 一般为0x7DF)
- 诊断应答发送报文(同一网络的每个E0U的ID不一样)

例:整车同一网络中有ECU A，B, C，D多个节点，假设他们的物理请求消息ID为0x701，0x702， 0x703， 0x704，响应消息地址分别为0x70A，0x70B，0x70C，0x70D，所有ECU的功能寻址ID为0x7DF。

**物理寻址时:**

0x701 0x10 0x01 (对ECU A进行诊断请求)
0x70A 0x50 0x01 xx xx хx (仅ECU A)

**功能寻址时:**

0x7DF 0x10 0x01 (对所有ECU进行诊断请求)
0x70A 0x50 0x01 xх xx xх xx (ECU A响应)
0x70B 0x50 0x01 xx xx xх xx (ECU B响应)
0x70C 0x50 0x01 xx xx xх xx (ECU C响应)
0x70D 0x50 0x01 xx xx хх xx (ECU D响应)

### 4. UDS常用诊断服务

- 诊断会话控制 DiagnosticSessionControl(0x10)
- ECU 复位 ECUReset(0x11)
- 安全访问 SecurityAccess(0x27)
- 通讯控制 CommunicationControl(0x28)
- 待机在线 TesterPresent(0x3E)
- 诊断故障码设置控制 ControlDTCSetting(0x85)
- 读DID 数据 ReadDataByIdentifier(0x22)
- 写DID 数据 WriteDataByIdentifier(0x2E)
- 清除故障码 ClearDiagnosticInformation(0x14)
- 读故障码信息 ReadDTCInformation(0x19)
- 通过DID 控制输入输出 Input0utputControlByIdentifier(0x2F)
- 例程控制 RoutineControl(0x31)
  （以下都是常用的升级服务）
- 请求下载 RequestDownload(0x34)
- 传输数据 TransferData(0x36)
- 请求数据传输退出 RequestTransferExit(0x37)

其他不常用的服务请参考IS014229文档

- **诊断会话控制 DiagnosticSessionControl(0x10)**

会话模式有默认会话模式(default session)和非默认会话模式(non-defaultsession)，非默认会话模式包含编程会话模式(ProgrammingSession)、拓展诊断会话模式(extendedDiagnosticSession)及其余会话模式。会话模式可以理解为诊断的功能模式，即在不同的会话模式下，能够支持不同的诊断功能，如在默认会话模式下，一般情况下不允许支持写服务(WriteDataByIdentifier0x2E)，也不允许支持请求下载服务(RequestDownload0x34)，而在拓展诊断会话模式下，就允许支持写服务(WriteDataByIdentifier0x2E)，在编程会话模代下,就可以支持(RequestDownload0x34)。

DiagnosticSessionControl(0x10)为控制切换各个会话模式的服务。

如下图，为三种常用会话模式的转换图

- 默认会话模式(default session 0x01)
- 编程会话模式(ProgrammingSession 0x02)
- 拓展诊断会话模式(extendedDiagnosticSession 0x03)

![20241120145328](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120145328.png)

ECU 在刚上电或者复位之后处于默认会话模式(0x01)，默认会话模式下发送10 03可以切换至拓展会话模式(0x03),**此时发送11 xx又会进入默认会话模式**，拓展会话模式发送10 02可以切换至编程会话模式(0x02)，而**在默认会话模式不可以直接跳转至编程会话模式**，若在默认模式会话模式直接发送10 02，此时ECU需要响应NRC为0x7E(subFunctionNotSupportedInActiveSession)的负响应，响应内容为7F 10 7E。

如果 ECU 处于非默认会话模式下，客户端发送10 01进入默认会话模式，ECU收到该请求后，ECU的安全访问状态切换到锁定状态，由ReadDataByPeriodicIdentifer(0x2A)服务配置的周期调度被禁止，通过CommunicationControl(0x28)和 ControlDTCSetting(0x85)进行的设置均被复位，即ECU初始化所有在非默认模式下激活的事件，设置和控制等操作，但不包括已经写入到非易失性存储位置的操作。**(就是当在非默认会话模式下操作的一些配置在回到默认会话模式时，这些配置都会被重置，但对于一些写入操作是不会被重置的)**

请求格式

![20241120150245](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120150245.png)
![20241120150706](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120150706.png)

正响应格式

![20241120150814](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120150814.png)

负响应格式

![20241120150837](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120150837.png)

- ECU 复位 ECUReset(0x11)

ECU复位ECUReset(0x11)是控制ECU端执行复位的服务。诊断仪发送11 01复位请求后，**ECU复位动作执行前，正响应需先发送给诊断仪，然后ECU执行复位操作**(不然复位了都发不出去了)，成功复位后，ECU 需进入默认会话模式。

请求格式

![20241120151023](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120151023.png)

正响应格式

![20241120151051](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120151051.png)

负响应格式

![20241120151304](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120151304.png)

* 安全访间 SecurityAccess(0x27)

安全访问SecurityAccess(0x27)，此服务是提供访问ECU内部数据或者出于安全因素需被限制的诊断服务的请求权限。常见的如读服务(0x22)读取非安全信息时能够直接读取，不需要利用27服务进行安全访问，而通过写服务(0x2E)写入数据时，则通常需要通过27服务进行安全访问才可以写，刷新程序也需要利用27服务通过相关的安全等级才能够对ECU进行程序下载，显然这些都是需要利用 27 服务进行权限管控，从而保障 ECU的安全可靠。

安全访问序列如下图所示，一共4步组成，

- 诊断仪先发送请求seed的报文(27 01)
- ECU响应seed(67 01 xx xx xx xx)
- 诊断仪根据返回的 seed 按照约定算法计算 key 值，并发送给ECU请求验证(27 02 xx xx xx xx)
- ECU收到请求之后，也按照约定的算法对该key进行校验，并给出响应，若计算一致，则为正响应(67 02)，否则为负响应(7F 27 NRC)

![20241120151806](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120151806.png)

ECU 若校验key一致，则ECU则切换安全状态至对应的解锁状态，此时在该解锁状态下能够支持的服务都应该可以工作。如果ECU已经处于解锁状态，此时诊断仪再次发送请求seed的报文，ECU应该响应seed为0的正响应。

seed请求的子服务值为奇数，对应的key请求验证的子服务值为该奇数加1，如27 01与27 02为一组安全等级，27 03与27 04为一组安全等级，27 11与27 12为一组安全等级。不同的安全等级由客户定义功能区分。

seed请求格式

![20241120152059](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152059.png)

seed正响应格式

![20241120152241](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152241.png)

key 请求验证格式

![20241120152331](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152331.png)

key 验证正响应格式

![20241120152409](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152409.png)

key 验证负响应格式

![20241120152501](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152501.png)

- 通讯控制 CommunicationControl(0x28)

通讯控制服务用于开启或关闭 ECU 对某些报文的发送或接收，如应用报文和网络管理报文等。
(例如诊断仪在维修希望ECU不要发了)。
请求格式

![20241120152646](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152646.png)

![20241120152720](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120152720.png)

正响应格式

| 序列号 |           参数名称           |       值       | 使用要求 |
| :----- | :--------------------------: | :------------: | :------: |
| 0      | Positive Response Service id |       68       |    M    |
| 1      |    Sub-Function Parameter    | 00,01,02,03-FF |    M    |

负响应格式

![20241120162843](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120162843.png)

- 待机在线 TesterPresent(0x3E)

该服务用于诊断仪端告知ECU诊断仪依然在线。该服务通常用于保持ECU处于非默认会话模式，由于ECU在S3server时间收不到诊断请求的话，ECU将会退回默认会话模式(default session)，所以诊断仪为了保持 ECU 处于非默认模式，需要周期性发送TesterPresent服务指令，周期时间需要小于S3server。

请求格式

![20241120163037](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120163037.png)
**当发送的是3E 00，如果是正响应的反馈就必须回。当发送的是3E 80，则不需要回。
当看到子服务为最高位为1，则是抑制正响应反馈，ECU就不需要给诊断仪回。**

正响应格式

![20241120163327](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120163327.png)

负响应格式

![20241120163419](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120163419.png)

- 诊断故障码设置控制 ControlDTCSetting(0x85)

诊断故障代码设置控制服务用于停止或重启ECU诊断故障代码的记录。

当通过该服务对故障码记录进行抑制操作后，若会话层时序参数超时从而ECU进入默认会话，或ECU执行复位操作后，诊断故障代码应该恢复记录。

当接收到诊断仪发送的清除诊断信息(0x14)服务后，ECU应重新开启诊断故障代码记录。

请求格式

![20241120164051](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120164051.png)

![20241120164105](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120164105.png)
**当是从扩展会话模式切换到默认会话模式时，开始扩展会话模式，此时ECU关闭故障码记录，当从扩展会话模式切换到默认会话模式时，此时ECU会重新开启故障码记录。**

正响应格式

![20241120164742](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120164742.png)

负响应格式

![20241120164801](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120164801.png)

- 读DID 数据 ReadDataByIdentifier(0x22)

根据 DID(标识符)读取数据服务用于从ECU存储器中读取由 DID所确定的数据记录值，其中DID为两个字节长度的数值。**(用户自己定义的，所以DID需要自己知道)**

IS014229中定义该服务的请求报文允许支持1个或者多个数据标识符，一般主机厂仅支持1个DID读取。下图报文格式也以1个DID作为讲解。

请求格式

![20241120164844](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120164844.png)

正响应格式

![20241120165209](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165209.png)

负响应格式

![20241120165238](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165238.png)

- 写DID 数据WriteDataByIdentifier(0x2E)

根据 DID写入数据服务允许诊断仪将数据写入由DID指定的内部存储单元。ECU应在数据写入成功后发送该服务的肯定响应。

请求格式

![20241120165416](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165416.png)

**byte1和byte2为用户自己定义 例如0x80 90，byte3和byte4为数据。**

正响应格式

![20241120165506](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165506.png)

负响应格式

![20241120165527](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165527.png)

- 清除故障码 ClearDiagnosticInformation(0x14)

正响应需在**诊断信息清除请求后**，ECU处理完成后发出，即使ECU没有存储的DTC，也需发出正响应报文。清除DTC的同时，所有DTC相关存储信息都应被清除。

请求格式

![20241120165626](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165626.png)

![20241120165647](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120165647.png)

正响应格式

![20241120170135](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170135.png)

负响应格式

![20241120170151](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170151.png)

- 读故障码信息 ReadDTCInformation(0x19)

请求格式

![20241120170224](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170224.png)

![20241120170248](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170248.png)

![20241120170305](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170305.png)

![20241120170329](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170329.png)

![20241120170347](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170347.png)

正响应格式

![20241120170526](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170526.png)

![20241120170551](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170551.png)

![20241120170613](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170613.png)

![20241120170638](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170638.png)

负响应格式

![20241120170703](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170703.png)

- 通过 DID 控制输入输出 Input0utputControlByIdentifier(0x2F)

该服务是用于通过 DID 来直接控制 ECU 对应的输入输出信号。

请求格式

![20241120170813](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170813.png)

![20241120170858](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170858.png)

**根据用户DID找到ECU哪一个输入输出，根据inputOutputControlParameter具体干什么。**

![20241120170931](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120170931.png)
**00:就是不控制Ecu,02：就是冻结当前状态，03：暂时调整**

正响应格式

![20241120171433](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120171433.png)

负响应格式

![20241120171501](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120171501.png)

- 例程控制 RoutineControl(0x31)
  诊断仪通过该服务来控制ECU执行响应的操作，函数什么的。

请求格式

![20241120171547](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120171547.png)

![20241120171600](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120171600.png)
**子服务具体让例程停止还是开始**

正响应格式

![20241120171616](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120171616.png)

**子服务后的是routineIdentifyer，这个是ECU自己定义的，用来区分不同的例程。**

负响应格式

![20241120172204](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172204.png)

- 请求下载RequestDownload(0x34)

请求格式

![20241120172257](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172257.png)

正响应格式

![20241120172330](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172330.png)

负响应格式

![20241120172351](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172351.png)

- 传输数据 TransferData(gx36)

请求格式

![20241120172428](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172428.png)

正响应格式

![20241120172559](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172559.png)

负响应格式

![20241120172539](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172539.png)

- 请求数据传输退出 RequestTransferExit(0x37)

请求格式
![20241120172649](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172649.png)

正响应格式

![20241120172710](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172710.png)

负响应格式

![20241120172733](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241120172733.png)

### 5. 总结

UDS诊断服务是汽车诊断使用最广泛的诊断服务，盖了非常多的诊断服务。
将来 UDS有趋势替代其他服务，成为真正的统一诊断服务。

## DCM模块

### 1. DCM 模块内部实现

诊断通信管理模块 DCM(Diagnostic Communication Manager)是实现 AutoSAR BSW 诊断功能的重要模块。确保**诊断数据流**并**管理诊断状态**，比如管理**诊断会话**(像上面的UDS诊断默认会话，编程会话，拓展会话都是在DCM内部实现的)和**安全状态**以及**诊断服务分配**等。

- DCM模块会检查当前诊断服务请求是否支持
- 确认诊断服务能否在当前会话等级以及安全等级执行
- 支持UDS服务和OBD服务
- DCM 提供了0SI层的5-7层，会话及应用层

![20241122144150](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122144150.png)

DCM内部由三大子模块组成:

- DSL:DiagnosticServer Layer 诊断服务层
1. 确保诊断请求和诊断应答的数据流

    - 接收PDUR的诊断请求，并把诊断请求发给DSD模块将DSD模块
    - 发送出来的诊断响应转发给PDUR块

2. 定时器管理 

    - S3定时器超时监控.**(ECU 在S3定时器内没有接收到诊断仪发送的报文，就会超时，ECU复位恢复到默认会话)**
    - P2定时器超时监控.**(诊断仪发送报文后，规定ECU好久反馈，就会超时，ECU复位恢复到默认会话)**

3. 管理会话状态、安全等级以及身份认证状态

- DSD:Diagnostic Server Dispatcher诊断服务分发

    1.检查诊断请求的合法性
    2.正响应抑制
    3.会话状态、安全等级校验
    4.分发诊断消息给到DSP去执行具体的诊断服务

- DSP:Diagnotic Server Processing诊断服务执行
    1.支持 UDS 服务，如0x10、0x22、0x2E等服务
    2.支持0BD服务，如0x01、0x02、0x03等服务

![20241122144618](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122144618.png)
**DEM是管理故障码的**

DCM 一些关键概念解释:
**Protocol**:指的是协议，DCM支持
**OBD ON CAN**
**UDS_ON_CAN**
**UDS_ON_IP**
**UDSONLIN**等协议，主要是UDS和OBD在不同总线上的实现。
**BufferList:**指的是 DCM 数据接收和传输的缓存
**ProtocalTiming:**定时器配置，S3P2定时器
**DsdServiceTable:**具体服务列表，支持哪些具体服务


### 2. DCM 模块和其他模块的交互

Dcm 模块作为诊断通信管理模块，和Dem、NVM、BswM、ComM、PduR模块都有交瓦。

Dcm 和PduR的交互

Dcm 通过内部的 DSL子模块和PduR进行交互，实现诊断报文传输：

![20241122145920](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122145920.png)

Dcm 和 DEM 的交互

DEM向DCM提供检索故障码相关的信息，比如0x14服务0x19服务，具体如下图:

![20241122150046](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122150046.png)

Dcm 和 ComM 的交互

当通信状态变化时，ComM请求启动或者禁用诊断通信功能

![20241122150525](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122150525.png)
DCM会话模式转变，请求ComM通信模式变化

![20241122151201](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122151201.png)
**注:对于ComM通信管理而言，会有多个User请求通信状态变化，比如DCM、SWC、NM 等，当DCM 请求关闭通信时，只是DCM的通信不使用了，不涉及其他User的请求，只有当所有User 都请求关闭通信时，ComM才会真正关闭某一路通信。**


**DCM和SWC的交互:**

有一些具体服务是需要SWC实现的，比如诊断I0控制的SWC，当DCM的31服务请求时，会去具体SWC执行控制操作，比如关闭1ed灯等

**DCM和BswM的交互:**

- 比如 0x28服务，DCM收到28服务通信控制时，会将通信模式改变转发给 BswM，BswM 根据模式变化执行对应的Action，去开启或者关闭通信
- 比如0x11服务时，也会通知到BswM，BswM根据不同的复位类型执行不同的 Action
DCM 和 SWC的交互:
### 3. 总结

DCM 由三个子模块组成，DSL DSD DSP，三个子模块都有属于自己的功能，共同完成诊断通信管理功能。DCM模块在诊断中起到最重要的作用，UDS和OBD服务均是在该模块中实现。


## OBD服务

### 1. 0BD 和 UDS 服务的区别

上一讲我们讲到了UDS服务，对于现在大部分汽车工程师可能接触到的都是UDS服务，在大部分的汽车电子ECU上，只需要UDS服务就足够了。

0BD(0n-Board-Diagnose)车载诊断，该服务也是DCM模块支持的一种服务主要是为了监控排放相关的系统，比如发动机和变速箱。

OBD服务定义了排放相关系统必须支持的诊断服务和数据格式，0BD服务底层数据传输可以走K线也可以走CAN总线，现在大部分都是走CAN总线。0BD和UDS是并列的一套应用层协议，对于与排放相关的ECU，通常既要支持UDS协议也要支持0BD 协议。

OBD协议是由IS0-15031规定，我们主要关注IS0-15031-5，关注0BD支持的具体服务即可。

**注意:其实 OBD服务都可以通过 UDS服务来实现，相信未来UDS服务会取代掉OBD 服务，称为真正的统一诊断服务。**

### 2. 0BD 有哪些服务

服务。0x01-0x0A就是对应的服务ID即SID。0BD支持0x01-0x0A 共10

![20241122151731](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122151731.png)

**Service 0l-Request Current Powertrain Diagnostic Data:**

该服务用于读取动力系统当前的诊断数据，比如某个传感器的状态、发动机转速、DTC数量、故障指示灯是否亮起等，命令格式是SID+若干PID(Parameter ID)。每个PID也是一个byte，所以理论上PID取值范围是0x00至0xFE，但是IS0-15031-5只明确定义了部分PID，其余的值都保留。问题来了:0BD定义了如此多的PID，那么某个ECU到底支持哪些PID，诊断仪是如何获知的呢?

实际上，PID 分为两类，

- 一类用于具体的PID 数据
- 而另一类则用来读取ECU支持哪些P1D

用于第二种目的的PID分别是0x00，0x20，0x40…读取其中一个ID后ECU会返回4个字节的结果，这4个字节中的每个bit表示其所对应的PID是否被支持。以下面这个例子来说明就很容易理解了:

服务ID为01 PID为00 00是用来读取该ECU支持哪些PID的

![20241122153337](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122153337.png)

诊断应答

![20241122153355](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122153355.png)

正响应诊断应答服务号也是0x01+0x40，第二个byte为PID 0x00，后面4个byte读出当前ECU支持哪些PID，比如10111111 从高位开始，为1的位代表支持对应PID，最高位为1，支持0x01，不支持0x02，支持0x03-0x08.**(第一位为1就代表支持0x01,第二位为1就代表支持0x02,第三到第八位都为1的话就代表支持0x03-0x08)。**

- Service 02-Request Powertrain Freeze Frame Data

一旦ECU确定了某个故障，就要把这个故障被确定时的相关状态信息“冻结”下来，即所谓的冻结帧，这些状态信息对车辆故障的确定非常重要，因为它们记录了车辆发生故障时的很多相关信息，这些状态信息数据必须在IS0-15031-5的PID列表中选择(与Service 01使用的PID列表相同)。02命令和01命令的使用方式非常相似，只不过02读取的是故障发生时的数据，而01读取的当前数据，数据格式和含义都是相同的。与01命令不同的是，02命令中多了一个frame 字节，如下图所示:
![20241122154511](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122154511.png)

0BD规定，用frame=0x00来代表读取冻结帧。如果主机厂想自己再定义些什么其他的帧，或者多定义几个冻结帧，则可以给frame分配上其他的编号。

需要指出的是，0BD只规定了ECU需要为一个DTC存储冻结帧，当ECU中同时存在多个DTC时，就要根据优先级来判定存储谁的冻结了。

- Service 03-Request Emission-Related Diagnostic Trouble Codes

服务03用于读取存储在ECU中的与排放相关的“confirmed”DTC(可以参见本专栏中“汽车控制器(ECU)中DTC的状态位”一文)，用法非常简单，它没有任何参数，诊断仪只需要发送03即可。下面两张图分别展示了03命令的请求和响应格式。

![20241122154804](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122154804.png)

响应

![20241122155033](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122155033.png)

在03命令的响应中，第2个字节表示DTC数量，后面每两个字节表示一个DTC。

- Service 04-Clear/Reset Emission-Related Diagnostic Information
 
04服务用于清空ECU中存储的与排放相关的DTC。除了DTC以外，以下的信息也要被清除。
![20241122155148](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122155148.png)

执行 04命令时被清理的信息

它的使用非常简单，请求是一个字节的04，响应是一个字节的44。只有在发动机没有运转的时候才可以执行这个服务，否则ECU应该给出NRC 0x22(条件不满足)来拒绝该服务。

- Service 05-Request 0xygen Sensor Monitoring Test Results

05服务用于读取氧传感器的状态，对于0BDonCAN来说不支持该服务，相应的功能由 06服务实现。

- Service 06-Request On-Board Monitoring Test Results for SpecificMonitored Systems

该服务用于请求对特定被监测系统的监测结果。0BD中定义了一个MID(MonitorID)的表格，来标识被监测系统。一个ECU不一定需要支持所有的MID，获知具体支持哪些MID的方法与01和02服务所使用的方法相同，也是先读取00，20，40等ID。06服务的命令格式是SID+若干MID，命令格式如下

![20241122155424](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122155424.png)

![20241122155558](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122155558.png)

06服务的response中，针对某一个MID，可能有多个TID(Test ID)，因为针对一个系统可能有多个测试项目。TID表格也在0BD中定义。06服务的response格式固定，每个MID的每个TID有6部分组成，可以在上面的例子中看出:

1. MID
2. TID
3. Unit And Scaling ID，用于标识这个 TID的测试内容是什么，比如电压、时间、计数器之类的。
4. Test Value，实际测量值
5. Min.Test Value，这个测量值的最小
6. Max.Test Value，这个测量值的最大值

- Service 07-Request Emission-Related Diagnostic Trouble CodesDetected During Current or Last Completed Driving Cycle

07服务也是获取DTC，但是它与03服务区别在于，它用于获取在当前以及上一个驾驶循环中出现为处于“pending”状态的DTC(可以参见本专栏中“汽车控制器(ECU)中DTC的状态位”一文)，而03服务获取的是confirmed DTC。

它的请求格式和响应格式如下两幅图:

![20241122160000](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122160000.png)

- Service 08-Request Control of On-Board System, Test or Component

08服务用于对系统进行控制，进行元件测试操作。它相当于UDS中定义的2F和 31服务。它的使用方法是SID+TID，注意这个TID与05和06服务的TID不同，在0BD中有一个专门给08服务使用的TID表格。

- Service 09-Request Vehicle Information

09服务用于读取车辆信息，它的命令请求格式是SID+若干InfoType，InfoType在OBD标准中有定义。并不是所有的InfoType都需要被支持，具体哪些InfoType被支持，可以采用和01服务相同的方法来获知，读取00，20，40等ID。比如InfoType=02代表17个ASCII的Vehicle Identification Number .

目前，UDS和0BD是两套应用层协议，而0BD所提供诊断服务其实属于UDS所提供服务的一个子集，理论上来说UDS中的诊断服务都可以实现0BD中的要求。为了降低同时需要实现两套协议的成本，所以标准化组织分配了IS027145(World-Wide Harmonized 0BD)这个标准号来将0BD与UDS 统一，使用UDS中的诊断服务来替代0BD相关的诊断服务。具体替换方案如下表:

![20241122160329](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241122160329.png)

比如，在0BD中，使用02，03，07分别读取confirmedDTC，DTC环境数据，pending DTc，而这些功能都可以通过UDS中的19服务来实现(配合上不同的状态掩码和读取参数)。
### 3. 0BD服务和UDS服务在DCM代码中如何实现

DCM 模块可以**既支持UDS服务，又支持0BD服务**。我们假设UDS服务和OBD服务底层都是通过 CAN总线数据传输。我们需要在DCM配置代码中做如下工作:

- 创建两个Protocol，分别为UDS和0BD
- 两个Protocol有不同的优先级，可以设置0BD优先级高于UDS，当产生诊断冲突时，0BD服务优先
- 建立UDS Service table和0BD Servicetable
- 建立0BD的PID1ist，即支持的PID

当前实现 0BD方案看到有两种:

1. 把OBD服务0x01-0x0A，也归属于 UDS 服务列表，所以只需要 UDS服务一个Prtotocol，这种并不是正规做法。
2. 0BD是完全独立的Protocol，一条消息PDU只能归属于一个Protocol,所以 OBD 不能复用 UDS服务的can报文。
**注:0BD 最好是和 UDS 使用不同的CAN总线。**
以上为经验所得，可能在不同产品上使用不同。

### 4. 总结

OBD服务适合尾气排放相关的诊断服务，使用场景不多，一般在燃油车的动力总成ECU 需要支持 0BD 和UDS两个服务。