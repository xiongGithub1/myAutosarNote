<!--
 * @Author: qinXiong
 * @Date: 2024-11-26 15:48:42
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-26 16:11:57
 * @Description: 
-->


# 操作系统OS

## 1. 为何需要使用操作系统 0S
首先，如果不使用操作系统0S，就相当于我们常说的“**裸机编程**”，也就是经常见到的手写单片机驱动和创建周期性任务。在简单场景和应用下，裸机编程确实可以满足要求，但是随着系统需求越来越复杂，就必须要使用**模块化设计方法以及多任务编程思想。**

当涉及多个**任务之间调度，任务状态切换，优先级，现场保护恢复，执行时间监控**等要求时，裸机编程就很难满足要求。因此就非常需要一个专门管理各个任务，管理内存的专用软件，因此操作系统0S就出现了。0S主要解决下面几个基本问题:

- 任务管理
- 内存管理
- 中断管理
- 异常管理
- 核间通信管理

其中**任务调度管理**是0S非常重要的组件，负责任务的切换。在单核系统中，多任务只能**串行执行**，通过**时间片轮转**的方法来切换任务。通过时钟中断或者软中断的方式来触发一次任务的调度，打断当前执行的任务，拿到CPU使用权，切换到新任务开始执行。

## 2. OSEK OS背景

传统汽车电子开发领域，早期使用的OS就是OSEK0S，其中OSEK是德文“Offemp Systeme und deren Schnittstellen fur die Elektronik imKrafttahrzeug”的缩写，含义是**汽车电子开发系统及接口**。OSEK OS是早期汽车电子领域使用**非常广泛的、可靠的、实时的、成本敏感的单核操作系统**。
说白了，欧洲汽车工业发展非常早，早期汽车ECU都是使用OSEK OS，那时候产品功能单一，只需要单核芯片就够了。后期AUTOSAR推出后，AUTOSAR OS就是在OSEK OS基础上进一步发展。

OSEK OS特点:

- 是静态配置和缩放的，用户静态指定所需的任务、资源、服务数量
- 支持在 ROM 中执行
- 支持应用任务的可移植性
- 操作系统所定义的动作可预见且可记录

以上几个特点，AUTOSAR OS也基本都有相关特性，这也是汽车车控OS典型的特点。

法国的汽车制造商雷诺后面也加入OSEK体系，并将法国工业使用的汽车分布式系统(VDX)也纳入到OSEK。后续产生了**OSEK/VDX规范**，由四部分组成:

- 操作系统规范(OSEK OS)
- 通信规范(OSEK COM)
- 网络管理(OSEK NM)
- 实现语言(OSEK Implementation Language)

**OSEK OS和AUTOSAR OS联系**

首先AUTOSAR OS是基于OSEK OS继承发展而来，OSEK OS的基本特点在AUTOSAR OS上都能得到满足，**AUTOSAR OS可以兼容OSEK OS，即OSEK OS上运行的应用程序也可以在 AUTOSAR OS上运行。**
AUTOSAR OS属于系统服务，但由于需要直接操作一些硬件资源，所以会**贯穿三层到硬件**。

![20241126160155](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126160155.png)

汽车电子 OS 发展经历了:
1. non-OSEK OS
2. OSEK 0S
3. AUTOSAR OS

**当前就处在AUTOSAR OS。**

AUTOSAROS除了继承0SEK0S的基础属性之外，还具有多个扩展性能:
- 调度表
- 内存保护
- 时间保护
- 多核支持

**AUTOSAR0S需支持的基本属性:**

- 静态配置以及伸缩扩展
- 实时性
- 基于任务优先级调度策略
- 运行时保护功能(内存、时间等)
- 中断优先级高于任务
- 中断处理机制
- 启动接口:StartOS()和StartHook()
- 关闭接口:ShutdownOS()和ShutdownHook()

**AUTOSAR OS的可扩展性**:

**AUTOSAR OS在OSEK OS的基础上为不同的用户提供四类不同功能安全等级的OS可裁剪类型，分别为SC1-SC4.**(SC:Scalability Classes，可伸缩的类型)

AUTOSAR OS的四种可裁剪类型分别为SC1-SC4，具体含义如下:

- SC1:OSEK OS+ Schedule Table;
- SC2:OSEK 0S + Schedule Table + Timing Protection;
- SC3:OSEK 0S + Schedule Table + Memory Protection;
- SC4:OSEK 0S + Schedule Table + Timing Protection + Memory Protection;**(现在主流)**

安全性来讲，SC4>SC3~=(约等于)SC2>SC1

![20241126161025](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126161025.png)

![20241126161039](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126161039.png)



## 3. 总结

OSEK OS作为**早期汽车电子**使用广泛的操作系统，已经将安全性考虑进去。随着汽车电子越来越复杂，芯片功能越来越强大，在OSEK OS基础上发展成AUTOSAR OS，并得到大规模应用。