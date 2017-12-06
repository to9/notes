
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

#### 虚拟机Ubuntu下安装QT开发环境
- Qt Creator 4.4.1 (Community)

#### 树莓派运行QT程序

