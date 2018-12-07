## Docker CE for Ubuntu

## 目录
* [1. OS requirements](#1)
* [2. Uninstall old versions](#2)
* [3. Install](#3)
* [4. Uninstall](#4)
****

注意：
如果是企业内网安装docker，需要注意设置代理地方，否则docker安装和使用过程中会有无法访问问题！

### 1. OS requirements
To install Docker CE, you need the 64-bit version of one of these Ubuntu versions:
* Bionic 18.04 (LTS)
* Xenial 16.04 (LTS)
* Trusty 14.04 (LTS)

### 2. Uninstall old versions

```bash
$ sudo apt-get remove docker docker-engine docker.io
```

### 3. Install
Install using the repository.

1. Update the apt package index:

```bash
$ sudo apt-get update
```

2.Install packages to allow apt to use a repository over HTTPS:

```bash
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

3. Add Docker’s official GPG key:

```bash
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

如果成功会出现OK！如果长时间无反应或失败，需要对当前shell设置代理。

```bash
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
curl: (28) Connection timed out after 300502 milliseconds
gpg: no valid OpenPGP data found.
```

```bashl
$ export https_proxy="https://xxxx:xxxx@proxy.lnsign.com:8080/"
```
```bash
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
OK
```

```bash
$ sudo apt-key fingerprint 0EBFCD88
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

4. Use the following command to set up the stable repository. 

```bash
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

5. Update the apt package index.

```bash
$ sudo apt-get update
```

6. Install the latest version of Docker CE, or go to the next step to install a specific version:

```bash
$ sudo apt-get install docker-ce
```

7. Verify that Docker CE is installed correctly by running the hello-world image.

```bash
$ sudo docker run hello-world
```

Unable to find image 'hello-world:latest' locally
docker: Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers).
See 'docker run --help'.

如果无法获取pull需要设置http代理。

```bash
$ sudo mkdir -p /etc/systemd/system/docker.service.d
$ sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf
```

```bash
[Service]
Environment="HTTP_PROXY=http://xxxx:yyyy@proxy.lnsign.com:8080/"
Environment="HTTP_PROXY=http://xxxx:yyyy@proxy.lnsign.com:8080/"
Environment="NO_PROXY=localhost,127.0.0.1,localaddress,.localdomain.com,10.10.163.114"
```

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl show --property=Environment docker
$ sudo systemctl restart docker
```

客户端基于证书访问docker服务器仓库
A custom certificate is configured by creating a directory under /etc/docker/certs.d using the same name as the registry’s hostname, such as localhost. All *.crt files are added to this directory as CA roots.

The following illustrates a configuration with custom certificates:

```
    /etc/docker/certs.d/        <-- Certificate directory
    └── localhost:5000          <-- Hostname:port
       ├── client.cert          <-- Client certificate
       ├── client.key           <-- Client key
       └── ca.crt               <-- Certificate authority that signed
                                    the registry certificate
```

创建证书存放目录

```bash
$ sudo mkdir -p /etc/docker/certs.d/10.10.163.114\:5000/
```

Docker certificates ca.zip

```bash
$ sudo cp ./ca.crt /etc/docker/certs.d/10.10.163.xxx\:5000/ca.crt
```

根据证书进行访问

```bash
$ sudo docker pull 10.10.163.xxx:5000/ubuntu14-x86_64:Ver0.3
```

### 4. Uninstall

1. Uninstall the Docker CE package:

```bash
$ sudo apt-get purge docker-ce
```

2.Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete all images, containers, and volumes:

```bash
$ sudo rm -rf /var/lib/docker
```

You must delete any edited configuration files manually.

#### 官网：
	https://docs.docker.com/install/linux/docker-ce/ubuntu/
	https://docs.docker.com/install/linux/docker-ce/ubuntu/
	https://docs.docker.com/engine/security/certificates/

