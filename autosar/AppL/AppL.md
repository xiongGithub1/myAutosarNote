<!--
 * @Author: qinXiong
 * @Date: 2024-11-26 16:30:27
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-26 16:46:13
 * @Description: 
-->

# AutoSAR 应用层


## 1.什么是 AppL 层。

AutoSAR Cp将软件架构分成三层:ARRL、RTE、BSW

![20241126161706](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126161706.png)

AppL就是应用层的意思，RTE就是运行时环境，BSW就是基础软件。这也使得汽车电子 AutoSAR 开发分成了应用开发和底层开发，应用开发工程师使用**MatLab**工具来开发应用层AppL层。

**注:其实大部分操作系统都是这样，比如非常成熟的Linux操作系统，Linux内核就相当于基础软件，用户开发Linux应用程序相当于APPL。**

**可以看出所有操作系统级别的软件架构均是如此，方便模块化编程和解。**

AutoSAR AppL层主要开发一些汽本ECU的业务逻辑程序。

## 2. AppL的组成

AppL层最重要的就是SWC(Software Componont)，从上面结构可以看出AppL层由多个SWC组成，而SWC就是具体一个业务功能。映射到代码中，一个SWC就是一个.c和一个.h文件。
这里软件工程将 AppL、RTE、BSW各作为一个文件夹，AppL下面有多个SWC，每个SWC包含了一个.c和.h文件。

**说的通俗一点就是，一个SWC就是于个应用功能模块，功能模块之间也是隔开的。**

![20241126163736](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126163736.png)

一个SWC内部由一个或者多个runable(运行实体)组成，**runable 映射在代码中就是一个函数**，一个应用功能模块内部肯定是有**一个或者多个函数的，也就是一个或者多个 runable。**

**举个简单的例子**:

我希望测试CAN通信，现在有CAN1和CAN2两路CAN总线，思路是这样的:

1、用一个SWC(CAN_INPUT)接收到CAN1一条报文
2、用一个SWC(CAN_HANDLE)将CAN1报文的信号赋值给CAN2信号
3、用一个SWC(CAN_OUTPUT)将CAN2报文发送出去

这就需要在应用层实现3个SWC,每个SWC内部有相应的函数(也就是runable)，runable放到task中运行，可以周期运行也可以事件触发运行。

代码呈现:

![20241126164123](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126164123.png)

## 3. 总结

AutoSARCP架构分层，其实就像常见的操作系统分层一样:

1. BSW就**像是操作系统内核**，实现了对所有**硬件资源的使用封装**，并向上提供接口。
2. RTE 是**实时运行环境**，处于BSW和APPL之间的分层，这一层非常灵活，有了这一层，真正实现APPL和BSW解耦，**BSW的开发和APPL的开发可以同步进行。**
3. APPL就是**用户应用程序**，每一个产品的应用程序都不一样，不需要关心底层BSW具体实现细节。

APPL开发大部分使用 Matlab 基于模型来开发，开发完成后生成.c .h文件，和RTE,BSW 进行打包，形成完成的工程。
