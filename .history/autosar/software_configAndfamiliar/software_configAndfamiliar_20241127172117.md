<!--
 * @Author: qinXiong
 * @Date: 2024-11-22 08:40:04
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-27 17:21:17
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
