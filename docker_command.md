## docker command

### 常用指令

##### 1. systemctl命令

```
systemctl daemon-reload                            <-- 重新载入配置文件
systemctl restart docker                           <-- 重启docker
systemctl show --property=Environment docker       <-- 显示docker配置Environment信息，http代理会用到
systemctl enable docker                            <-- 设置开机启动
systemctl disable docker                           <-- 设置禁止开机启动
systemctl restart docker.service                   <-- 重启docker.service
systemctl stop docker.service                      <-- 停止docker.service
systemctl status docker                            <-- 显示docker.service状体信息
```

##### 2. 镜像仓库

```
docker pull                                        <-- 从镜像仓库中拉取或者更新指定镜像
docker push                                        <-- 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库
docker login                                       <-- 登陆到一个Docker镜像仓库
docker logout                                      <-- 登出一个Docker镜像仓库
docker search                                      <-- 从Docker Hub查找镜像
```

##### 3. 系统信息与版本

```
docker --version                                   <-- 查看docker版本
docker info                                        <-- 显示 Docker 系统信息，包括镜像和容器数
```

##### 4. 管理

```
docker run                                         <-- 创建一个新的容器并运行容器
docker start                                       <-- 启动一个或多个已经被停止的容器
docker stop                                        <-- 停止一个运行中的容器
docker restart                                     <-- 重启容器 
docker exec                                        <-- 在运行的容器中执行命令
docker rm                                          <-- 删除一个或多少容器
```

##### 5. 容器操作

```
docker ps                                          <-- 显示当前正在运行的容器
docker images                                      <-- 列出本地镜像
docker container ls                                <-- 查看容器
```

##### 6. 文件操作

```
docker cp                                          <--容器内外复制文件 
```

### 命令示例：

```
docker pull 192.168.1.106:5000/centos:6.8          
docker push 192.168.1.106:5000/centos 
docker run -itd 10.10.163.114:5000/ubuntu14-x86_64:Ver0.3 /bin/bash
docker start vinfast
docker exec -it fast /bin/bash
docker rm centos_test                              
docker cp /opt/1.txt 96f7f14e99ab:/ws
docker container ls                                
``` 
 
 ```
docker run -idt -v /media/cross_compile:/ws --name="fast" 10.10.163.114:5000/ubuntu14-x86_64:Ver0.3 /bin/bash
"-i"   Keep STDIN open even if not attached
"-d"   Run container in background and print container ID
"-t"   Allocate a pseudo-TTY
"-v"   Bind mount a volume
"/media/cross_compile:/ws"创建一个宿主机与docker容器共享问价夹，宿主机文件夹必须使用绝对路径/media/cross_compile， docker容器文件ws，这样我们就可以在容器内使用宿主机文件夹内容，省去了复制文件的操作。
"--name="fast" 给容器起一个好记名字， 之后就可以使用这个名字对容器进行start/stop/rm/exec等操作。
"/bin/bash" 指定运行开启shell 
```

退出使用exit或者Ctrl+D, 若想返回终端，保持容器运行Ctrl+p然后Ctrl+q。
