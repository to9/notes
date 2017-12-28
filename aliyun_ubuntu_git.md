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
```
git@iZ2zeeutrttwr14jmp2cpcZ:~/.ssh$ ls
authorized_keys
```

9. 生成git推送和拉取权限文件，生成rsa秘钥对文件
生成RSA秘钥对时候需要输入密码，这个密码一定要记住后面要使用，而且以后的git克隆推送都需要
```
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/git/.ssh/id_rsa): gaodongzi@126.com
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in gaodongzi@126.com.
Your public key has been saved in gaodongzi@126.com.pub.
The key fingerprint is:
SHA256:uT0cfuz+i8WFGMPVW3pAHp6NntFJdrVsfXY7NuVgY0Q git@iZ2zeeutrttwr14jmp2cpcZ
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
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ls
gaodongzi@126.com  gaodongzi@126.com.pub
```

生成的文件*.pub为RSA公钥文件， 另外一个为RSA私钥文件。