## Hexo在win7下安装


### 准备工作
- git安装
- Node.js安装

### 安装Git


### 安装Node.js

简介
		简单的说Node.js就是运行在服务端的JavaScript。Node.js是一个基于Chrome V8引擎的JavaScript运行环境。
Node.js使用了一个事件驱动、非阻塞式I/O的模型，使其轻量又高效。Node.js最新版已经自带包管理器npm

- 下载Node.js
官网https://nodejs.org

- 安装
选择默认安装即可，npm包管理器在安装Node.js时已经一起默认安装了。

- 测试是否安装成功
C:\Users\Administrator>node -v
v9.3.0


### 安装Hexo

- 找一个你喜欢的盘，新建一个文件夹如Blog

- 选中blog文件夹右键，选择Git Bash

- 输入指令开始安装
npm install -g hexo

hexo 会安装到这里

  C:\Users\Administrator\AppData\Roaming\npm\node_modules
  
  C:\Users\Administrator\AppData\Roaming\npm-cache
  
- 初始化hexo到你指定的目录， 也可以cd到目标目录，执行hexo init
hexo init <folder>

- 生成静态页面
hexo generate
生成静态页面至hexo\public\目录，当你修改文章Tag或内容，不能正确重新生成内容，可以删除hexo\db.json后重试，还不行就到public目录删除对应的文件，重新生成。

- 本地启动
hexo server 启动本地服务
浏览器输入http://localhost:4000就可以看到效果，进行文章预览调试

注：
win7下hexo安装成功，并且正确运行，但是localhost:4000不能访问。可能你的端口被占用了，
1. 当次有效
		hexo启动的时候可以不使用默认的4000端口，我们可以指定其他端口， 例如hexo s -p 4001
2. 永久修改
vim node_modules/hexo-server/index.js, 修改port到指定端口。


使用Hexo
1. 写文章
+hexo new [layout] "postName" #新建文章

### 更换主题
- git clone https://github.com/iissnan/hexo-theme-next themes/next


