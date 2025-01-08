<!--
 * @Author: qinXiong
 * @Date: 2025-01-08 20:50:10
 * @LastEditors: xiongGithub1&&qx20001119@163.com
 * @LastEditTime: 2025-01-08 21:54:42
 * @Description: 
-->

# 学习建议
## 1.7.Autosar学习指导点拨
1.7.1搞清楚自己定位
1.7.2前导知识和技能要有
1.7.3要有架构意识
1.7.4、要记好专业概念词汇
1.7.5、要抓本质而轻工具
(1)AutosarCP到AP
(2)Autosar各版本细节差异到思想统一
(3)Autosar各种商业解决方案
(4)自动生成代码和手工编辑代码的切割和结合 

## 2.1.Autosar的细节学习资源
2.1.1、官网
(1)https://www.autosar.org/
(2)官网浏览:重点是standards下的几种Autosar标准
2.1.2、标准文档
(1)标准文档浏览
(2)本课程选择19-11作为参考
(3)演示使用:general/AUTOSAR_EXP_LayeredsoftwareArchitecture.pdf
2.1.3、开源代码
(1)arctic_core_autosar cp_project介绍
(2)源码工程建立和浏览
2.1.4、网络资料
(1)2.6.1中推荐的2个技术blog
(2)用日标关键词去搜索，淘资料。

## 2.2.AutoSarCP分层和分模块详解
2.2.1、专有词汇
(1)Autosar标准起的名字和概念
(2)专门缩写，有特定含义，交流和理解的关键\
(3)建议建立个人专有词汇表
2.2.2、读标准pdf演示
(1)逐行解读
(2)配合翻译软件
2.2.3、AutosarCP的3个粒度分层
(1)分3层:App、RTE、BSW
(2)BSW分3层4部分:service层，ECU abstraction层，MCAL，复杂驱动
(3)BSW纵向分模块

系统服务层，存储，加密，离板通信，通讯，IO抽象层，复杂驱动层

## 2.4.AutosarCP各软件分层解读
2.4.1、MCAL
2.4.2、ECU抽象层
2.4.3、复杂驱动
2.4.4、Services层
2.4.5、RTE
2.4.6、AppL

## 2.7.AutoSarCP软件层的几个概念
2.7.1、types of services： System Services 、Memory Services、Crypto Services Off-Board Communication Services 、Communication Services、I/O Hardware Abstraction、Complex Drivers

2.7.2、internal driver：MCAL
2.7.3、external driver：ECU抽象层
2.7.4、interface：ECU抽象层
2.7.5、handler
2.7.6、manager
2.7.7、libraries

## 3.1.AutosarCP操作系统学习概述
3.1.1、基本点
(1)AutosarCP的0S是RT0S的一种，但是和典型工业消费级的如freertos等有差异，也多了很多学习点
(2)AutoSarcP的0S基于0SEKOS，又做了一些扩展。所以先学0SEKOS，再针对性学扩展点
(3)0SEK和Autosar只规定了0S的特性和行为，
实现是解决方案商的事儿，要么购买要么自研
3.1.2、OS核心价值
(1)Autosar系统中"干活"的是Application和BSW，而OS是支撑体系，不干“功能性的活儿”.
(2)OS主要与Application和BSW打交道，将后2者的运行entities分配到OS的task中运行实现，这是OS对CPU的管理
(3)OS还对硬件中断有管理，实现ISR机制，这也是为了服务Application和BSW。os设计时彼此都是独立的，都是标准API，通过配置生成、RTE来对接
(4)Autosar体系下，application、bsW、os设计时彼此都是独立的，都是标准API，通过配置生成、RTE来对接。
3.1.3、本课程的目标和价值
(1)从设计哲学和方法论上理解AutosarCP的0s与Application和BSW，如何协作工作。进而更深入理解Autosar
(2)结合文档和讲解，深度理解0S的核心概念，如调度机制、任务管理、优先级、alarm、resources等。
(3)结合文档和代码，对AutoSarcP体系的实现有直观认识，应聘和工作中获得竞争优势。

3.1.4、本课程如何讲如何学
(1)资料1:《AUTOSAR Classic操作系统-重难点.pptx》
(2)资料2:《IS017356-3翻译版.docx》
(3)资料3:Autosar官方文档中0S部分
(4)资料4:开源代码arctic core
(5)学习要求:有一定0S基础，学过autosar前面2个课程，有一定单片机嵌入式和C语言基础