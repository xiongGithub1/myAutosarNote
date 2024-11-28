<!--
 * @Author: qinXiong
 * @Date: 2024-11-22 08:40:04
 * @LastEditors: Qxiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-28 16:37:52
 * @Description: 
-->


# 各软件

## 各软件的安装



## S32DS 新建工程、编译调试下载、导入工程详细介绍

[CSDN网址]https://blog.csdn.net/luobeihai/article/details/131820271


- 查看局部变量显示被优化
![20241127163444](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127163444.png)


## 芯片锁死原因以及解锁
### 能够连接上芯片
1. 先打开Jlink.exe

![20241127163959](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127163959.png)
2. 查看连接状态

![20241127164147](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127164147.png)

搜索出了芯片内核问题就不大
![20241127164239](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127164239.png)


3. 输入解锁命令
``` 
//解锁命令
unlock kinetis
//擦除命令
erase
 ```
![20241127164451](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127164451.png)

### 连接不上芯片

1. 先打开Jlink.exe
2. 直接输入解锁命令```unlock kinetis```
3. 输入连接命令 ```connect```
4. 选择Jwag 然后一路回车
5. 最后输入擦除命令 ```erase```

6. 选择Jflash进行烧录
![20241127164955](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127164955.png)

新建一个s32k144的工程进行及连接

然后将生成的二进制文件拖入然后点击Target—>Manual Programming ->Program 点击多次不行后 在点击Erase Chip,擦除成功后再点击Program 烧入。
![20241127165327](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127165327.png)
![20241127165543](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127165543.png)
 

 ## GPIO操作

 引脚配置
 ![20241127165907](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127165907.png)

![20241127170129](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127170129.png)

1.中断标志位2.中断触发方式3.io引脚复用4.锁5.推挽使能6.上拉下拉电平7.初始电平8.数字滤波字段
![20241127170633](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127170633.png)

点击生成工程
![20241127171107](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127171107.png)

选中函数进行编写代码，操作单个io
![20241127171857](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127171857.png)

操作多个io

![20241127172127](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127172127.png)

读取按键引脚电平

![20241128113101](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128113101.png)


## adc外设

![20241128135055](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128135055.png)

![20241128135637](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128135637.png)

编写读取adc函数以及定义的全局变量
![20241128135848](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128135848.png)

## Ftm定时器中断

配置定时器
![20241128140547](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128140547.png)

初始化
![20241128141051](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128141051.png)

中断函数

while实现
![20241128141340](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128141340.png)


## 按键外部中断

配置按键中断

![20241128142425](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128142425.png)

中断函数实现

![20241128142302](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128142302.png)


## 看门狗

配置看门狗
![20241128143042](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143042.png)

实现
![20241128143228](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143228.png)

![20241128143340](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143340.png)

![20241128143116](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143116.png)

看门狗初始化
![20241128143541](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143541.png)

看门狗中断函数
![20241128144049](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128144049.png)

电源复位检测
![20241128143916](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128143916.png)

## CAN总线

CAN总线接口定义

![20241128144323](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128144323.png)

can配置

![20241128144442](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128144442.png)

![20241128144708](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128144708.png)

使用变量

![20241128144813](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128144813.png)

can0初始化
![20241128145439](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128145439.png)
在回调函数里能判断CAN收发状态
![20241128152922](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128152922.png)
发送报文
![20241128152859](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128152859.png)

接收报文

![20241128153128](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128153128.png)



## lin总线

1. lin配置，lin是通过串口协议实现的。

![20241128153641](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128153641.png)

![20241128153817](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128153817.png)

2. 响应了帧头 1.从机开始接收数据 2.主机开始接收数据从机发送数据3.从机睡眠。
![1732779930243](image/software_configAndfamiliar/1732779930243.png)

3. 发送接收数据数组
![20241128154802](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128154802.png)

- 主机发送从机接收数据帧头

![20241128154856](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128154856.png)

![20241128154923](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128154923.png)

- 主机发送主机接收数据

![20241128155046](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155046.png)

    当前的id等于主机接收数据
![20241128155119](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155119.png)

- 主机发送睡眠指令
从机休眠的话就唤醒，唤醒的状态下就休眠
![20241128155242](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155242.png)

![20241128155440](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155440.png)

4. 添加定时器

![20241128155714](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155714.png)

![20241128155916](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128155916.png)

5. 超时函数的回调函数
![20241128160017](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128160017.png)

![20241128160129](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128160129.png)
![20241128160229](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128160229.png)
![20241128160252](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128160252.png)

## mcal安装配置 软件下载与安装

**MCAL**
MCAL是Microcontroler Abstraction Layer的缩写，即微控制器抽象层，位于AUTOSAR构架中的BSW(基础软件)层，是可以直接驱动芯片外设和访问微处理器内寄存器的的底层驱动。由芯片开发商提供，目的是使上层软件与底层硬件分离，来达到开发的标准化与通用化。

![20241128163814](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241128163814.png)