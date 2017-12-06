
## 树莓派zerow + Qt5.9.2

#### 搭建环境
- Windows7 32-bit
- [Oracle VM VirtualBox](https://www.virtualbox.org/)
- [Ubuntu 16.04.3 Desktop (64-bit)](http://releases.ubuntu.com/16.04/ubuntu-16.04.3-desktop-amd64.iso.torrent?_ga=2.253121097.1318740821.1512544173-1116467422.1493000235)
- [qt-everywhere-opensource-src-5.9.2.tar.xz](http://download.qt.io/archive/qt/5.9/5.9.2/single/qt-everywhere-opensource-src-5.9.2.tar.xz)
- [2017-04-10-raspbian-jessie.zip](https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-04-10/2017-04-10-raspbian-jessie.zip)
- [树莓派交叉编译器](https://github.com/raspberrypi/tools)

#### 虚拟机安装
windows7 32-bit 安装虚拟机VirtualBox.

#### 虚拟机安装Ubuntu 16.4

#### Ubuntu下交叉编译器设置

- 解压压缩文件
<div align=left><img width="713" height="27" src="https://github.com/to9/notes/blob/master/raspberry_qt5.9.2_images/tools-master_1.png"/></div>
<div align=left><img width="713" height="52" src="https://github.com/to9/notes/blob/master/raspberry_qt5.9.2_images/tools-master_2.png"/></div>
<div align=left><img width="713" height="86" src="https://github.com/to9/notes/blob/master/raspberry_qt5.9.2_images/tools-master_3.png"/></div>

  该目录中存在4个文件夹，本例使用gcc-linaro-arm-linux-gnueabihf-raspbian 或 gcc-linaro-arm-linux-gnueabihf-raspbian-x64。前者对应32位系统后者对应64位系统。
  arm-bcm2708hardfp-linux-gnueabi
  gcc-linaro-arm-linux-gnueabihf-raspbian
  arm-bcm2708-linux-gnueabi
  gcc-linaro-arm-linux-gnueabihf-raspbian-x64

- 查看交叉编译编译器版本, 命令行输入arm-linux-gnueabihf-gcc –v 
<div align=left><img width="713" height="86" src="https://github.com/to9/notes/blob/master/raspberry_qt5.9.2_images/tools-master_4.png"/></div>

#### qt源码编译

- 创建交叉编译qt所需根目录opt
````
root@gaodong-VirtualBox:~# mkdir ~/opt
root@gaodong-VirtualBox:~# cd ~/opt
````
- 解压树莓派系统镜像文件
```
root@gaodong-VirtualBox:~/opt# unzip 2017-04-10-raspbian-jessie.zip
```

- 创建挂载目录和挂载Raspbian系统镜像文件

````
root@gaodong-VirtualBox:~/opt# mkdir /mnt/rasp-pi-rootfs

root@gaodong-VirtualBox:~/opt# fdisk -l 2017-04-10-raspbian-jessie.img 
Disk 2017-04-10-raspbian-jessie.img: 4 GiB, 4285005824 bytes, 8369152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x402e4a57

Device                          Boot Start     End Sectors Size Id Type
2017-04-10-raspbian-jessie.img1       8192   92159   83968  41M  c W95 FAT32 (LBA)
2017-04-10-raspbian-jessie.img2      92160 8369151 8276992   4G 83 Linux
```

92160 * 512 = 47185920
```
root@gaodong-VirtualBox:~/opt# mount -o loop,offset=47185920 2017-04-10-raspbian-jessie.zip /mnt/rasp-pi-rootfs
```
挂载成功后/mnt/rasp-pi-rootfs目录下会出现树莓派系统文件系统目录
```
root@gaodong-VirtualBox:/mnt/rasp-pi-rootfs# ls
bin  boot  debian-binary  dev  etc  home  lib  lost+found  man  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

#### 虚拟机Ubuntu下安装QT开发环境
- Qt Creator 4.4.1 (Community)

#### 树莓派运行QT程序

