
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
```
root@gaodong-VirtualBox:~/opt# unzip tools-master.zip
root@gaodong-VirtualBox:~/opt/tools-master/arm-bcm2708# ls
arm-bcm2708hardfp-linux-gnueabi   arm-rpi-4.9.3-linux-gnueabihf   gcc-linaro-arm-linux-gnueabihf-raspbian-x64
arm-bcm2708-linux-gnueabi         gcc-linaro-arm-linux-gnueabihf-raspbian

```

  该目录中存在4个文件夹，本例使用gcc-linaro-arm-linux-gnueabihf-raspbian 或 gcc-linaro-arm-linux-gnueabihf-raspbian-x64。前者对应32位系统后者对应64位系统。
  
  arm-bcm2708hardfp-linux-gnueabi
  
  gcc-linaro-arm-linux-gnueabihf-raspbian
  
  arm-bcm2708-linux-gnueabi
  
  gcc-linaro-arm-linux-gnueabihf-raspbian-x64

- 查看交叉编译编译器版本, 命令行输入arm-linux-gnueabihf-gcc –v 

#### 挂载树莓派系统

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
```
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
bin  boot  debian-binary  dev  etc  home  lib  lost+found  man  media  mnt  opt  
proc  root  run  sbin  srv  sys  tmp  usr  var
```

#### qt源码编译
- 解压qt源码
```
root@gaodong-VirtualBox:~/opt# tar -xvf qt-everywhere-opensource-src-5.9.2.tar
```
- 编译qtbase
```

```

#### 虚拟机Ubuntu下安装QT开发环境
- Qt Creator 4.4.1 (Community)

#### 树莓派运行QT程序



#### 交叉编译：
[QT官方教程](https://wiki.qt.io/Raspberry_Pi_Beginners_Guide)

[QT官方教程翻译版](http://blog.diveinedu.com/%E6%A0%91%E8%8E%93%E6%B4%BE%E4%B8%8Aqt5%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E7%A7%BB%E6%A4%8D%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97/)
