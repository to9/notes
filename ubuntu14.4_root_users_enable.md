
## Ubuntu14.4开启root账户登录

#### 1: 设置root账户密码
sudo passwd root

#### 2: 修改文件50-unity-greeter.conf
修改文件usr/share/lightdm/lightdm.conf.d/50-unity-greeter.conf

文件内容如下：
```
[SeatDefaults]
user-session=ubuntu
greeter-session=unity-greeter
greeter-show-manual-login=true
all-guest=false
```
#### 3: reboot重启即可
