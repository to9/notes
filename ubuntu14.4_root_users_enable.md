
## Ubuntu14.4开启root账户登录

#### 1: 设置root账户密码
```
dong@dong-VirtualBox:~#  sudo passwd root
输入新的 UNIX 密码:
重新输入新的 UNIX密码：
passwd: 已成功更新密码
```

#### 2: 修改文件50-unity-greeter.conf
```
dong@dong-VirtualBox:~# vim usr/share/lightdm/lightdm.conf.d/50-unity-greeter.conf
```

文件内容如下：
```
[SeatDefaults]
user-session=ubuntu
greeter-session=unity-greeter
greeter-show-manual-login=true
all-guest=false
```
#### 3: reboot后提示 "读取/root/.profile时发现错误"，错误信息.

```
root@dong-VirtualBox:~# vim .profile
```
- .profile 文件最后一行修改为

    tty -s && mesg n || true

#### 4: reboot重启修改完成

