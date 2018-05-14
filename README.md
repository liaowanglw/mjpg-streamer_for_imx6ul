# mjpg-streamer_for_imx6ul
---  

## 一 环境  
主机环境:ubuntu 18.04 LTS   
目标机:NXP i.MX6UL（FETMX6UL-C）需要连接上网络  
目标机内核环境:3.14.38  
交叉编译工具链:arm-fsl-linux-gnueabi-gcc 4.6.2  
摄像头：罗技C270  

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
* 1 将源码顶层及plugins目录下所有子层目录的Makefile中的`CC = gcc`改为`CC= arm-fsl-linux-gnueabi-gcc`。  
可使用命令`grep gcc * -nIR`查看    
* 2 修改plugins/input_uvc下的Makefile文件  
`vim mjpg-streamer/plugins/input_uvc/Makefile`  
修改:  
CFLAGS += -O1 -DLINUX -D_GNU_SOURCE -Wall -shared -fPIC  
为：  
CFLAGS += -O1 -DLINUX -D_GNU_SOURCE -Wall -shared -fPIC -I /home/mjpg-streamer_for_imx6ul/jpeg/include  

修改：  
(CC)(CFLAGS) -o @inputuvc.cv4l2uvc.lojpegutils.lodynctrl.lo(LFLAGS)  
为：  
(CC)(CFLAGS) -L /home/mjpg-streamer_for_imx6ul/jpeg/lib -o @inputuvc.cv4l2uvc.lojpegutils.lodynctrl.lo(LFLAGS)  
（注：/home/mjpg-streamer_for_imx6ul/jpeg为jpeg-8b编译安装后自定义的路径）  

* 3 编译  
`make`  
编译成功后会生成mjpg_streamer、input*.so、output*.so等文件。  

* 4 将整个mjpg_streamer目录拷贝到目标板下。  

---  
### 3 测试  
进入目标板的mjpg_streamer目录，使目标板连上网络。  
连接上usb摄像头，使用`/dev/video*`查看设备号，一般为/dev/video1。  
输入命令:  
`./mjpg_streamer -i "./input_uvc.so -d /dev/video1" -o "./output_http.so -w ./www"`  
如果工作正常，摄像头工作指示灯亮起，终端上最终打印:  
`o: www-folder-path...: ./www/`  
`o: HTTP TCP port.....: 8080`  
`o: username:password.: disabled`  
`o: commands..........: enabled`  
在chrome或者firefox浏览器（不可使用IE）地址栏输入http://192.168.1.111:8080/?action=stream 就可以看到图像。  

---  

### 4 附注  
* 1 192.168.1.111为目标板的ip地址，可以通过`ifconfig`查看；  
* 2 8080为端口地址，可以在`-o`命令后双引号内加入`-p`制定，例：将端口设为8090:  
`./mjpg_streamer -i "./input_uvc.so -d /dev/video1" -o "./output_http.so -p 8090 -w ./www"`  
* 3 如果需要修改输出的分辨率，可以在`-i`命令后的双引号内加入`-r`制定，  
默认有以下分辨率：  
QQVGA  160x120  
QCIF   176x144  
CGA    320x200  
QVGA   320x240  
CIF    352x288  
PAL    720x576  
VGA    640x480  
SVGA   800x600  
XGA    1024x768  
HD     1280x720  
SXGA   1280x1024  
UXGA   1600x1200  
FHD    1920x1280  
例：将输入分辨率改为HD：  
`./mjpg_streamer -i "./input_uvc.so -d /dev/video1 -r HD" -o "./output_http.so -w ./www“`  
或者直接输入需要的分辨率，例如将输入分辨率改为320x240：  
`./mjpg_streamer -i "./input_uvc.so -d /dev/video1 -r 320x240" -o "./output_http.so -w ./www”`   
  
### 更多设置和指令可以输入`./mjpg_streamer --help`或者在start.sh中查看。  

---  
### 注：以上为在同一局域网内查看输出视频，如需外网查看，自行检索端口转发相关内容对路由器进行设置。  
### 如需要查看截屏，则在浏览器地址的action=后将stream改为snapshot：  
#### 例：  
#### http://192.168.1.111:8080/?action=snapshot


`









