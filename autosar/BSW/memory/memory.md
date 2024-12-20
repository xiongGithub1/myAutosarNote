<!--
 * @Author: qinXiong
 * @Date: 2024-11-25 11:53:24
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-26 13:56:49
 * @Description: 
-->


# 存储功能

### 1、存储基础知识

**RAM** 又称**随机存取存储器**，存储单元的内容可以按照需要随机取出或者存入，存取数据比较快。这种存储器在断电时，会丢失其存储内容，所以一般是CPU运行时会把程序从ROM拷贝到RAM里面执行。所以一般RAM是作为和CPU直接交换数据的内部存储器，也叫主存或者内存。

**SRAM**是Static RAM的缩写，它具有静态存取功能。静态随机存取存储器采取多重晶体管设计，不需要刷新电路即能保存它内部存储的数据，特点是高性能、低集成度、速度快，一般在MCU或者S0C会**内置**一小块SRAM。

**DRAM** 是动态随机存取存储器，每隔一段时间固定对DRAM刷新充电一次，否则内部数据会消失。像现在的内存条 DDR都属于DRAM。

**ROM**全称是Read0nlyMemory，顾名思义，它是一种只能读出事先所存的数据的固态半导体存储器。ROM存储的数据掉电不会丢失，可以用来存储各种固化程序和数据。最初的ROM是不能编程的，出厂是什么内容就永远是什么内容，非常不灵活。后面出现了prom，可以自己写入一次，写错了，只能再换一片，后面又出现了可多次擦除写入的EPROM，每次擦除都要把芯片拿到紫外线上照一下。

**EEPROM**(Erasable Programmable Read-0nly Memory)是在EPROM的基础上进一步发展形成的**电可擦除可编程只读存储器**，不需要擦除的时候去照紫外线，它可以按照字节操作，但是集成度不高、价格比较贵。

**FLASH** 又称为闪存，属于广义的EEPROM，因为它也是电擦除的ROM，它和EEPROM最大的区别就是，FLASH只能按照扇区(block)操作，而EEPROM可以按照字节操作。FLASH的电路结构比较简单，同样容量占芯片面积较小，成本比EEPROM低很多。FALSH分为NORFLASH和NAND FLASH。

**NOR FLASH**数据线和地址线分开，可以实现ram那样随机寻址功能，也就是说程序可以在 nor flash上面直接运行，不需要拷贝到ram中。

**NAND FLASH**同样是按块擦除，但是数据线和地址线复用，不能利用地址线随机寻址。
### 2、AUTOSAR 存储

图中是AUTOSAR的BSW整体架构，其中存储功能从下层到上层依次是存储驱动、存储硬件抽象、存储服务。

![20241125152249](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241125152249.png)

**注意:Autosar 的存储模块主要是针对用户存放data数据的，也就是那些会经常变化，需要实时存储起来的数据。比如一些故障码，当ECU检测到一些故障码就需要实时存储起来，在维修的时候就可以通过工具读取出来分析。**

Autosar的存储模块介质主要是两种:EEPROM和FALSH仿EEPROM，分为片内和片外存储，因此就有2*2=4种存储方式:

- 主芯片片内FLASHEEPROM
- 主芯片片内EEPROM
- 板载片外FLASH仿EEPROM
- 板载片外EEPROM

**注意:前面讲了EEPROM和EASH最大的不同就是EEPROM可以操作的最小单位是字节，也就是可以直接擦除编程一个字节。FASH的最小擦除单元是扇区，最小编程单元是page 页，TC397芯片的DFLASH的逻辑扇区就有4K大小，page页大小是8字节。**

当前AUTOSAR项目开发中，使用最多的就是TC397芯片，该芯片是使用397芯片的DFLASH来模拟EEPROM，用于NVM存储服务使用。

下图是AUTOSAR存储模块具体分层:

![20241125153052](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241125153052.png)

**NVRAM Manager**:简称NVM，是应用层访问非易失性数据的唯一接口，提供非易失数据的管理服务。这一层会统一按block编号，不关心底层是什么存储类型。

**Memory Abstraction Interface**:简称 MemIf，主要作用就是将读写的信息解耦，分别分派给EEPROM或FLASH。

**EEPROM Abstraction**:简称Ea，EEPROM的抽象层，主要作用就是进一步封装片外或片内EEPROM驱动，给上层提供统一的API接口。

**FlashEEPROM Emulation**:简称Fee，Flash模拟EEPROM的抽象层

**EEPROM Drv**:片内EEPROM的驱动

**FlashDrv**:简称fls，片内flash驱动

这个图很清晰的描述了 AUTOSAR 存储的几种情况:

**片内存储**:
1、NVM->MemIf->Ea->EEPROM DrV->EEPROM
2、NVM->MemIf->Fee->FLS->FLASH(TC397上使用的方案)
**片外存储**:
1.NVM->MemIf->Ea->EEPROM Drv->SPI->EEPROM
2.NVM->MemIf->Fee->FLS->SPI->FLASH

### 3、TC397的Flash 编程

当前AUTOSAR项目用到的主要芯片是英飞凌的TC397芯片，介绍一下TC397芯片上面硬件 FLASH 相关知识。

- TC397芯片存储分为PFLASH(ProgramFlashMemory)和DFLASH(Data公Flash memory)
- TC397有5个3MB大小PFx(PF0...PF4)和一个1MB大小的PF5。每个PFx被划分为 1024KB大小的物理扇区，每个物理扇区又被划分为16KB大小的逻辑扇区(Logical Sector)
- TC397有两个数据闪存存储区DFLASHO和DFLASH1，**就是用这个DFLASH来模拟eeprom，来作为autosar的存储服务使用的**。DFLASH0还包含了用于数据保护的用户配置块(UCBs,UserConfigurationBlocks)和1个配置扇区(CFS)，用户无法直接访问该配置扇区。
- DFLASH逻辑扇区可以配置4KB或者2KB，DFLASH的页有8字节组成，**也就是 DFLASH 最小擦除单元为4/2K，最小编程单元为8字节**。
- PFLASH逻辑扇区16KB，PFLASH的页有32字节组成，**也就是PFLASH最小擦除单元为 16K，最小编程单元为 32 字节**。

![20241125155742](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241125155742.png)

![20241125155820](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241125155820.png)


## 存储服务之Fee换页机制

### 1、AUTOSAR 存储服务基础知识回顾

老规矩，先把 AUTOSAR存储模块的架构图摆出来，方便分析。

![20241126083521](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126083521.png)

存储基础知识请参考上一篇文章:
在TC397芯片部署AUTOSAR，存储模块软件架构分层依次是:NVM->MemIf->Fee->FLS->FLASH(TC397上使用的方案)
NVM 是存储服务层，也就是存储模块最上层，该层是对外提供的接口。

MemIf是接口层，区分下面是Fee还是Ea。

Fee是Flash模拟EEPROM 的抽象层

Fls是flash驱动。在TC397中有一块 DFLASH,AUTOSAR规范中是通过 DFLASH模拟EEPROM的形式将数据存到DFLASH中的。

**Page**:DFLASH最小编程单元，大小为8Byte。意味着即使只想写入1一个字节的数据，也会将对应的8字节数据重新写入。这就要求Fee软件层需要先读取出相应Page的数据(到内存中)，(在内存中)修改其中的1个字节，再将8Byte写入。

**Logical Section**:逻辑扇区，擦除的最小单元，由若干个Page构成，配置成2K，就相当于一次擦除最少擦除2K大小。

**Physical Section**:物理扇区，由多个逻辑扇区组成。

**Data Flash**:可以由多个物理扇区组成，我们也称物理扇区为 BANK，所需要写入的数据会最终写入到物理扇区中，一个物理扇区写满了就需要新的物理扇区，但是物理扇区是有限的，不能一直有新的BANK，针对同一个数据如果多次写入，其实对于程序而言只有最后一次是有效的，因此如何擦除无用数据并循环使用物理扇区就是解决这个问题的关键，这就是**FEE换页机制**。

### 2、Fee 换页机制

换页机制可能针对不同的芯片，有一些区别，但是大体原理都是一样。

![20241126085210](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126085210.png)

- 第一次写入BL0CK1，Block1地址状态为有效(应用层只看到block,操作block)
- 第一次写入BL0CK2，Block2地址状态为有效
- 第二次写入BL0CK1，将第一次写入的Block1的地址状态置为无效，同时第二次写入的地址是有效。
- 当APP请求读取BL0CK1的数据时，只会读取地址有效的数据

**因此，如果针对同一个BLOCK数据多次写入，并不是将原来的内容覆盖，而是在扇区中写入新的数据，而将老的数据置为无效。**

**为什么对存在记录的数据不能采用覆盖原来数据的方式呢?**

如果采用覆盖的方式替换原来的旧有数据(针对同一BLOCK)，“覆盖”这个动作可以理解为先擦除原有数据再在原地址写入新数据，这种操作从理论上是可行的。但实际上却有很多问题

正如上面我们介绍过，擦除的最小单元是逻辑扇区(2k/4k)，写入的最小单元是Page(8Byte)，逻辑扇区要比PAGE大很多，如果用“覆盖的”方式意味着这个逻辑扇区中只能存在这一个BLOCK(避免擦除时将其他BL0CK DATA除掉。这么做会造成大量的空间浪费。

另外，Data Flash是有擦除寿命的，频繁的擦除是会减少D-FLASH的使用寿命。

擦除是最耗时的，每次BL0CK写入都要擦除原有数据再写入的话，会让效率很低。**而新增写入不需要每次擦除(会统一擦除)，每次只需要直接写入，在整个 BANK 写满后进行换页的时候再统一擦除。**
鉴于此，AUTOSARFLS/FEE的实现策略并未采用这种“覆盖“的方式
#### 换页机制(Bank Swap)

正如前面介绍，AUTOSAR FLS/FEE的实现策略采用的是新增的方式，而非覆盖的方式，那么随着数据写入的次数增多，当前物理扇区势必存在写满的时刻，那么就需要一个新的物理扇区，但由于物理扇区的个数是有限的，不能一直有新的 BANK，同时针对同一个BLOCK如果写入多次，对程序有用的数据只有最后一次，因此如何擦除无用的数据循环使用物理扇区是解决此问题的关键，这就引出了FEE/FLS换页机制(BankSwap)

![20241126115404](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126115404.png)

![20241126115548](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126115548.png)

![20241126115606](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126115606.png)

- **将所有 Valid 的数据拷贝到新的 Bank**
- **将原来 Bank 整体擦除**
- **将擦除的 Bank 作为新的 Bank 供下一次换页使用**

**Note:这是2个BANK的换页策略，理论上可以有N个BANK，不过换页机制大
体相近**

#### 换页时BLOCK写入请求

可能有人会问如果在换页过程中，有新的BLOCK写的请求该如何处理，首先，针对 BL0CK请求分为2种类型，imediate/Normal。对于immediate的写入请求，需要打断擦除动作，立刻将新的数据写入到NewBank中(有的FEEDriver可能会写入到“第3块BANK”)，原则上对于immediate WriteRequest 数据是不需要等换页完成的。对于Normal的写入请求，则需要等待换页完成才可以写入，因此**可能出现block写入的时间会比较长的情况，在设计的时候需要考虑到此种情况**

**Note:在NVM Block配置中如果Priority为0，则此BLOCK的请求为immediate request 反为Normal Request**


### 3、如何设计 BANK 大小

正如前面的介绍，BANK换页的工作原理，可以有如下总结

![20241126134534](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241126134534.png)

**优点和确定反了**

**NOTE:一般D-FALSH的会规定最多擦除次数**

那么我们应该如何设计BANK的大小呢?

1. 我们的ECU 都是有**设计使用时长**的，因此首先BANK的大小需要满足ECU的设计使用时长的，假设，BANK>30K就可以满足
2. 在这里我们需要估算出最多可能有**多少数据写入**，每隔一段时间又会有多少增量数据，进而推算出我们**换页的频率**
3. 在满足上一步的前提下，在根据ECU的特性，估算ECU所能接受的最大换页时长，正如前面说过的，对手**NORMAL Request**需要等待换页完成，因此写入时长会被拉长假设，BANK<60K就可以满足
4. 综上单个BANK(更严谨的说法是单次擦除的所有BANK总大小，因此当我们存在多个BANK时，换页可能发生在写满N个BANK才会触发)的大小就是在30K~60K之间就可以。
5. 算出了BANK的大小，再结合整体D-FLASH的大小，我们就能估算出大概需要几个 BANK 了。

### 4、总结

当FEE作为AUTOSAR存储模块的核心软件层，这一层需要考虑到**FLASH使用寿命、BLOCK 稳定写入、换页时长、启动换页阈值、BANK大小**等关键功能设计。AUTOSAR作为车载软件的标准，在很多模块设计上都会考虑到实际应用场景，满足车载软件的高标准要求。