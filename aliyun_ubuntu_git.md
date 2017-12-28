
## Ubuntu16.4 x64 详细服务器上的Git架设

当前运行用户为root

#### 1: 知识普及 git，git-core, gitosis

#### 2. Git使用区分客户端git，服务器端git

#### 3. 服务器上Git协议 本文实例使用的是ssh

#### 4. 环境确认，是否装ssh，git， 以ssh已经安装好为例子，(阿里云ubuntu主机已经安装好ssh)

#### 5. 安装git
```
root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get update
root@iZ2zeeutrttwr14jmp2cpcZ:/# apt-get install git
```

查看git版本
```
root@iZ2zeeutrttwr14jmp2cpcZ:/# git --version
git version 2.7.4
```

#### 6. 为服务器版本库创建单独git用户
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
