Ubuntu16.4 x64 详细服务器上的Git架设
当前运行用户为root，

1. 知识普及 git，git-core, gitosis

2. Git使用区分客户端git，服务器端git

3. 服务器上Git协议 本文实例使用的是ssh

4. 环境确认，是否装ssh，git， 以ssh已经安装好为例子，(阿里云ubuntu主机已经安装好ssh)

5. 安装git

root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get update
root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get install git

查看git版本
root@iZ2zeeutrttwr14jmp2cpcZ:/# git --version
git version 2.7.4

6. 为服务器版本库创建单独git用户


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

切换到git用户home目录下会多出来刚创建的用户git
root@iZ2zeeutrttwr14jmp2cpcZ:/home# ls
admin  git


7. 查看用户跟目录隐藏文件夹是否有.ssh文件夹, 如果没有则创建.ssh目录
root@iZ2zeeutrttwr14jmp2cpcZ:/home# su git
git@iZ2zeeutrttwr14jmp2cpcZ:/home$ cd
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile


git@iZ2zeeutrttwr14jmp2cpcZ:~$ mkdir .ssh
git@iZ2zeeutrttwr14jmp2cpcZ:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile  .ssh

8. 在git用户下查看.ssh目录下时候有authorized_keys文件, (如果不出以为的话肯定是没有的 我搽...)
进入.ssh目录新建authorized_keys文件，至此我们接下来进行该文件中需要填写每个git客户端需要的公钥数据， 那这个数据那来呢，让我们接着看。
git@iZ2zeeutrttwr14jmp2cpcZ:~/.ssh$ ls
authorized_keys



9. 生成git推送和拉取权限文件，生成rsa秘钥对文件
生成RSA秘钥对时候需要输入密码，这个密码一定要记住后面要使用，而且以后的git克隆推送都需要

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


生成的文件*.pub为RSA公钥文件， 另外一个为RSA私钥文件。

9.1 在win下使用Git安装自带工具Puttygen.exe生成*.ppk文件
对于需要使用RSA秘钥对生成来源, 你可以使用win生成也可使用linux生成，
本实例是在linux下生成并通过ssh工具到处到win下。
gaodongzi@126.com, gaodongzi@126.com.pub
运行Puttygen.exe 
	--> Conversions 
		--> Import Key 选择刚生成的gaodongzi@126.com 私钥文件 输入生成秘钥时候的秘钥-
			-> Save private key 保存为gaodongzi.ppk文件

gaodongzi.ppk文件用作git克隆推送代码时候需要的秘钥文件。
 

10.把生成的gaodongzi@126.com.pub公钥文件数据复制到git用户根目录隐藏文件夹.ssh里面的authorized_keys文件中，注意复制时候要保证文件一致。


11.在我们服务器上为git创建代码仓，以为这个是我们之后需要把代码推送拉取得地方
我把代码仓放在服务器/opt目录下.切换到超级用户su root下创建git目录, 在git下及时使用sudo mkdir git 也是权限没有。
root@iZ2zeeutrttwr14jmp2cpcZ:/opt# mkdir git
root@iZ2zeeutrttwr14jmp2cpcZ:/opt# cd git
root@iZ2zeeutrttwr14jmp2cpcZ:/opt/git# git init --bare project.git
root@iZ2zeeutrttwr14jmp2cpcZ:/opt/git/project.git# ls
branches  config  description  HEAD  hooks  info  objects  refs


12. windows下克隆刚建立的版本库
至于windows下git使用这篇文章暂时先不介绍，自行补脑后再继续。你也可以再linux下直接测试。
git@139.10.107.141:/opt/git/project.git, 不用多想ip地址肯定是假的
成功克隆服务器版本库后，修改版本库里面文件然后在推送，(如果不出意外的话,推送不上去， 我搽.....)

13. 任务尚未完成还需继续...
使用git用户登录查看/opt/git目录权限
git@iZ2zeeutrttwr14jmp2cpcZ:/opt$ ls -l
total 4
drwxr-xr-x 3 root root 4096 Dec 28 14:55 git

切换到root用户对git目录进行权限修改
root@iZ2zeeutrttwr14jmp2cpcZ:/opt# chown -R git:git git
root@iZ2zeeutrttwr14jmp2cpcZ:/opt# chmod 777 git
root@iZ2zeeutrttwr14jmp2cpcZ:/opt# ls -l
total 4
drwxrwxrwx 3 git git 4096 Dec 28 14:55 git

再切换会git用户查看权限，这下文件夹git具有读写权限了。我们可以放下使用了。
git@iZ2zeeutrttwr14jmp2cpcZ:/opt$ ls -l
total 4
drwxrwxrwx 3 git git 4096 Dec 28 14:55 git


到此...在服务器上部署Git算完成了
如果是多人使用，我么还需要gitosis来管理没个人的秘钥。如果你没疯那让我们继续...




参考：
https://git-scm.com/book/zh/v1/服务器上的-Git-在服务器上部署-Git
