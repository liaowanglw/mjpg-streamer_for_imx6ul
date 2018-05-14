# mjpg-streamer_for_imx6ul
---  

## 一 环境  
主机环境:ubuntu 18.04 LTS   
目标机:NXP i.MX6UL（FETMX6UL-C）  
目标机内核环境:3.14.38  
交叉编译工具链:arm-fsl-linux-gnueabi-gcc 4.6.2  

---  

## 二 目录说明  
* 1 jpeg-8b:libjpeg库  
* 2 mjpg-streamer：mjpg-streamer主目录  

---  

## 三 使用说明  
### 1 libjpeg动态库移植  
`cd mjpg-streamer_for_imx6ul`  
`mkdir jpeg`(或自命名为其他名字其他路径)  

`cd jped-8b`  
`./configure --prefix=<路径>/jpeg --host=arm-linux`  
`make`  
`make install`  
编译安装后在jpeg目录下会生成bin、include、lib、share四个目录。  
拷贝lib目录下的文件到目标板的/lib目录下。  

---
### 2 移植mjpg-streamer  
`cd mjpg-streamer`  
将源码顶层及plugins目录下所有子层目录的Makefile中的`CC = gcc`改为`CC= arm-fsl-linux-gnueabi-gcc`  





