<!--
 * @Author: qinXiong
 * @Date: 2024-11-26 16:13:59
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-26 16:27:56
 * @Description: 
-->


# AutoSAR 应用软件层  

## 1.什么是 RTE 层。

AutoSAR Cp将软件架构分成三层:ARRL、RTE、BSW

![20241126161706](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126161706.png)

AppL就是应用层的意思，RTE就是运行时环境，BSW就是基础软件。

有了RTE层的存在可以解耦AppL和BSW层，让两者可以同步开发，最后集成到一起。RTE为应用提供运行时环境。

RTE的作用有点像电话接线员，可以将一个SWC的信息通过RTE接到其他SWC或者BSW上。RTE具有管理这些信息的功能。
## 2.RTE 层的作用。

1. 通信功能(ECU内部(通过全局变量)和跨ECU(通过报文))

    S/R通信(Sender-Receiver)用于SWC之间、SWC与BSW之间交换数据
    C/S通信(Client-Server)用于SWC之间、SWC与BSW之间的函数调用，客户端调用服务器提供的函数

2. 模式管理:用于SWC之间、SWC和BSW模块之间关于运行模式的交互 例如ECU电源状态，各种通信总线的模式
3. 数据一致性保护，

- IRV(Inter RunnableVariables)，用于SWC内部，Runnable之间的数据交换。
- Exclusive Areas(EAs)，临界区保护，封装内核提供的临界区保护机制(原子操作、关中断，Spinlock，Resource)为SWC和RTE内部功能提供数据一致性保护

4. 调度RTE对Runnables运行支撑

![20241126162405](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126162405.png)

- 通过 RTE 给runnable 提供触发事件。通过 RTE 来实现这个触发和调用runnable
- 通过 RTE 给 runnable 提供所需资源。将runnable 需要的一些资源通过接口传输给它
- 将BSW和SWC做隔绝。因此OS和runnables也被隔绝了，runnable的运行条件由 RTE 提供，不能由OS直接提供



## 3. 总结。

RTE 作为autosar 不可或缺的一层，这一层代码在在工具链配置中，将 BSW 和应用层映射好，就会自动生成RTE代码。主要提供了上述4类机制。