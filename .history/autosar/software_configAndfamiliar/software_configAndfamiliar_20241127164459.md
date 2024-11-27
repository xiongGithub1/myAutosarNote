<!--
 * @Author: qinXiong
 * @Date: 2024-11-22 08:40:04
 * @LastEditors: xiong&&2307975018@qq.com
 * @LastEditTime: 2024-11-27 16:44:59
 * @Description: 
-->


# 各软件

## 各软件的安装



## S32DS 新建工程、编译调试下载、导入工程详细介绍

[CSDN网址]https://blog.csdn.net/luobeihai/article/details/131820271


- 查看局部变量显示被优化
![20241127163444](https://cdn.jsdelivr.net/gh/xiongGithub1/picGoUpload/image/20241127163444.png)


## 芯片锁死原因以及解锁

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

4. 