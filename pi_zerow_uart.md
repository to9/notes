
## 开启树莓派zerow的Uart功能

#### 1: 系统版本查看
```
root@raspberrypi:~# lsb_release -a
No LSB modules are available.
Distributor ID: Raspbian
Description:    Raspbian GNU/Linux 8.0 (jessie)
Release:        8.0
Codename:       jessie
```

#### 2: 40Pin定义
<div align=left><img width="285" height="382" src="https://github.com/to9/notes/blob/master/images/pi_40pin.png"/></div>

#### 3: 配置树莓派zerow串口
```
root@raspberrypi:~# raspi-config
```

树莓派配置菜单选择：

    5 Interfacing Options     --Configure connections to peripherals
    P6 Serial		  --Enable、Disbale shell and kernel messages on the serial connection

    Would you like a login shell to be accessible over serial?        No
    Would you like the serial port hardware to be enabled?            Yes

    The serial login shell is disabled
    The serial interface is enabled
    
#### 4: minicom安装
minicom 类似windows串口调试助手, 可以选择安装.
```
root@raspberrypi:~# apt-get install minicom
```

运行minicom软件，该软件默认波特率为115200, 1停止位，无奇偶校验。如需帮助CTRL-A Z查看.
```
root@raspberrypi:~# minicom -D /dev/ttyS0
```

注意： 树莓派Zerow是带蓝牙和wifi模块的，ttyAMA0是蓝牙使用，ttyS0用户使用， 如树莓派3B、zerow用户使用ttyS0。



