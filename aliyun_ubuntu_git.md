
Ubuntu16.4 x64 详细服务器上的Git架设

当前运行用户为root，

1. 知识普及 git，git-core, gitosis

2. Git使用区分客户端git，服务器端git

3. 服务器上Git协议 本文实例使用的是ssh

4. 环境确认，是否装ssh，git， 以ssh已经安装好为例子，(阿里云ubuntu主机已经安装好ssh)

5. 安装git
```
root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get update
root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get install git
```

查看git版本
```
root@iZ2zeeutrttwr14jmp2cpcZ:/# git --version
git version 2.7.4
```

6. 为服务器版本库创建单独git用户
```
root@iZ2zeeutrttwr14jmp2cpcZ:/home# adduser git
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
```
root@iZ2zeeutrttwr14jmp2cpcZ:/home# ls
admin  git
```

7. 查看用户跟目录隐藏文件夹是否有.ssh文件夹, 如果没有则创建.ssh目录
```
root@iZ2zeeutrttwr14jmp2cpcZ:/home# su git
git@iZ2zeeutrttwr14jmp2cpcZ:/home$ cd
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile

git@iZ2zeeutrttwr14jmp2cpcZ:~$ mkdir .ssh
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile  .ssh
```

8. 在git用户下查看.ssh目录下时候有authorized_keys文件, (如果不出以为的话肯定是没有的 我搽...)
进入.ssh目录新建authorized_keys文件，至此我们接下来进行该文件中需要填写每个git客户端需要的公钥数据， 那这个数据那来呢，让我们接着看。

9. 生成git推送和拉取权限文件，生成rsa秘钥对文件,生成RSA秘钥对时候需要输入密码，这个密码一定要记住后面要使用，而且以后的git克隆推送都需要


生成的文件1.pub为RSA公钥文件， 另外一个为RSA私钥文件。

9.1 在win下使用Git安装自带工具Puttygen.exe生成*.ppk文件
对于需要使用RSA秘钥对生成来源, 你可以使用win生成也可使用linux生成，
本实例是在linux下生成并通过ssh工具到处到win下。
gaodongzi@126.com, gaodongzi@126.com.pub
运行Puttygen.exe 
	--> Conversions 
		--> Import Key 选择刚生成的gaodongzi@126.com 私钥文件 输入生成秘钥时候的秘钥-
			-> Save private key 保存为gaodongzi.ppk文件

gaodongzi.ppk文件用作git克隆推送代码时候需要的秘钥文件。
 

