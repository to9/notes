## 详细Git服务器架设 - 阿里云( Ubuntu16.4 x64 )


## 目录
* [1. 知识普及](#1)
* [2. Git服务器/客户端区别](#2)
* [3. Git的通信协议](#3)
* [4. 环境确认](#4)
* [5. Git安装](#5) 
* [6. 服务器创建Git用户](#6)
* [7. 确认git用户.ssh文件存在与否](#7)
* [8. authorized_keys文件](#8)
* [9. Git用户密钥生成/使用](#9) 
* [10.  为git用户设置SSH验证](#10)
* [11. 创建初始化代码仓](#11)
* [12. win下测试版本库](#12)
* [13.git目录权限](#13)
* [14. 自动部署](#14)

****

### [1. 知识普及]

	git，git-core, gitosis

### 2. Git服务器/客户端区别
	使用区分客户端git，服务器端git

### 3. Git的通信协议
	协议服务器上Git协议 本文实例使用的是ssh

### 4. 环境确认
	，是否装SSH，Git， 以SSH已经安装好为例子，(阿里云Ubuntu主机已经安装好SSH)
- 当前SSH是否安装 **( :o: )**
```bash
root@h2ze245d:/# ssh
usage: ssh [-1246AaCfGgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-E log_file] [-e escape_char]
           [-F configfile] [-I pkcs11] [-i identity_file] [-L address]
           [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
           [-Q query_option] [-R address] [-S ctl_path] [-W host:port]
           [-w local_tun[:remote_tun]] [user@]hostname [command]
```
- 当前Git是否安装 **( :x: )** 
```bash
root@h2ze245d:/# 
The program 'git' is currently not installed. You can install it by typing:
sudo apt-get install git
```

### 5. Git安装
先运行apt-get update, update作用是从/etc/apt/source.list文件中定义的源中去同步包的索引文件，根据索引检查更新，获取的是软件的包的一些	                  状态信息从而保证要接下来下载的是最新版本.

```bash
root@h2ze245d:/# apt-get update
root@h2ze245d:/# apt-get install git
```
### 6. 服务器创建Git用户
```bash
root@h2ze245d:/home# adduser git
Adding user `git' ...
Adding new group `git' (1001) ...
Adding new user `git' (1001) with group `git' ...
Creating home directory `/home/git' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for git
Enter the new value, or press ENTER for the default	
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] y
```

切换到git用户home目录下会多出来刚创建的用户git
```bash
root@h2ze245d:/home# ls
admin  git
```

### 7. 确认git用户.ssh文件存在与否
	查看用户跟目录隐藏文件夹是否有.ssh文件夹, 如果没有则创建.ssh目录
```bash
root@h2ze245d:/home# su git
git@h2ze245d:/home$ cd
git@h2ze245d:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile

git@h2ze245d:~$ mkdir .ssh
git@h2ze245d:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile  .ssh
```

### 8. authorized_keys文件
在git用户下查看.ssh目录下时候有authorized_keys文件, (如果不出意外的话肯定是没有的 我搽...)进入.ssh目录新建authorized_keys文件，至此我们接下来进行该文件中需要填写每个git客户端需要的公钥数据， 那这个数据那来呢，让我们接着看.
```bash
git@h2ze245d:~/.ssh$ ls
authorized_keys
```

### 9. Git用户密钥生成/使用
生成git推送和拉取权限文件，生成rsa秘钥对文件， 生成RSA秘钥对时候需要输入密码，这个密码一定要记住后面要使用，而且以后的git克隆推送都需要,生成的文件*.pub为RSA公钥文件， 另外一个为RSA私钥文件。
```bash
git@h2ze245d:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/git/.ssh/id_rsa): key123@126.com
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in key123@126.com.
Your public key has been saved in key123@126.com.pub.
The key fingerprint is:
SHA256:uT0cOuz+i8WFGMfV83pAHp6N0tFJ7rVsfXY715pgY0m git@h2ze245d
The key's randomart image is:
+---[RSA 2048]----+
|             oE++|
|           . ==*B|
|            + @B@|
|         .   BoX*|
|        S . . +*o|
|         = o ...o|
|        . = o o  |
|           + o   |
|           .+.o. |
+----[SHA256]-----+
git@h2ze245d:~$ ls
key123@126.com  key123@126.com.pub
```

使用cat指令把公钥key123@126.com.pub中的内容，插入在authorized_keys文件尾.
```bash
git@iZ2zeeutrttwr14jmp2cpcZ:~/.ssh$ cat key123@126.com.pub >>authorized_keys
```

在win下使用Git安装自带工具Puttygen.exe生成*.ppk文件
对于需要使用RSA秘钥对生成来源, 你可以使用win生成也可使用linux生成，
本实例是在linux下生成并通过ssh工具到处到win下。
key123@126.com, key123@126.com.pub
运行Puttygen.exe 
	--> Conversions 
		--> Import Key 选择刚生成的key123@126.com 私钥文件 输入生成秘钥时候的秘钥-
			-> Save private key 保存为key123.ppk文件

key123.ppk文件用作git克隆推送代码时候需要的秘钥文件。
 

### 10.为git用户设置SSH验证
把生成的key123@126.com.pub公钥文件数据复制到git用户根目录隐藏文件夹.ssh里面的authorized_keys文件中，注意复制时候要保证文件一致。

### 11.创建初始化代码仓
我们服务器上为git创建代码仓，这个是我们用来存储客户端push操作后存放代码的地方，我把代码仓放在服务器/opt目录下.切换到超级用户su root下创建repo_server目录。git用户没有权限在opt下创建目录,如需要git用户在opt目录下创建目录需要root改opt目录权限。
```bash
root@h2ze245d:/opt# mkdir repo_server
root@h2ze245d:/opt# cd repo_server
root@h2ze245d:/opt/repo_server# git init --bare hexo.git
root@h2ze245d:/opt/repo_server/hexo.git# ls
branches  config  description  HEAD  hooks  info  objects  refs
```

### 12. win下测试版本库
至于windows下git使用这篇文章暂时先不介绍，自行补脑后再继续。你也可以再linux下直接测试。
git@139.10.107.141:/opt/repo_server/hexo.git, 不用多想ip地址肯定是假的
成功克隆服务器版本库后，修改版本库里面文件然后在推送，(如果不出意外的话,推送不上去， 我搽.....)

### 13. git目录权限
任务尚未完成还需继续...， 使用git用户登录查看/opt/repo_server目录权限
```bash
git@h2ze245d:/opt$ ls -l
total 4
drwxr-xr-x 3 git git 4096 Dec 28 14:55 repo_server
```
切换到root用户对git目录进行权限修改， 改仓库项目文件夹repo_server为git
```bash
root@h2ze245d:/opt# chown -R git:git repo_server
root@h2ze245d:/opt# chmod 777 repo_server
root@h2ze245d:/opt# ls -l
total 4
drwxrwxrwx 3 git git 4096 Dec 28 14:55 repo_server
```

再切换会git用户查看权限，这下文件夹git具有读写权限了。我们可以放下使用了。
```bash
git@h2ze245d:/opt$ ls -l
total 4
drwxrwxrwx 3 git git 4096 Dec 28 14:55 repo_server
```

到此...在服务器上部署Git算完成了
如果是多人使用，我么还需要gitosis来管理没个人的秘钥。如果你没疯那让我们继续...

### 14. 自动部署
如果Git需要实现自动部署功能的话，我们需要在代码仓hooks目录下必须有个脚本文件，至于需要那种类型脚本文件可以参看git官方说明。
在这里我们主要实现的功能是当客户端代码推送(push)完成后，服务端Git仓会自动clone一份客户端所有代码到我们需要的目录，这也就是web自动部署功能。hooks目录下post-receive类型脚本就是我们所需要的功能。脚本里面内容根据我么自己需求进行编写。

注意点:
- ssh生成密钥应该在git用户下进行
- git权限问题
- post-receive 文件应该具有执行权限
- 我们将要拉取存放内容的文件夹必须具有写入权限

```
git@iZ2zeeutrttwr14jmp2cpcZ:/opt/repo_server/hexo.git/hooks$ ls -l
-rwxrwxrwx 1 git git  101 Dec 30 13:03 post-receive

#!/bin/bash
rm -rf /opt/repo_local/hexo
git clone /opt/repo_server/hexo.git /opt/repo_local/hexo
```


#### 参考：
	https://git-scm.com/book/zh/v1/服务器上的-Git-在服务器上部署-Git
