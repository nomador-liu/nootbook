# docker

## 安装

```bash

1.
sudo apt-get update

2.
sudo apt-get install ca-certificates curl

3.
sudo install -m 0755 -d /etc/apt/keyrings

4. 
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

5.
sudo chmod a+r /etc/apt/keyrings/docker.asc

6. 
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

7.
sudo apt-get update

8. 
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

9. 测试是否安装成功
sudo docker run hello-world

# 苹果docker run hello-world

```





## docker拉取与运行

```bash
# 1. 拉取一个Ubuntu镜像
sudo docker pull ubuntu:22.04

# 2. 镜像交互式运行 -it
docker run --rm -it ubuntu:22.04 bash
```

> ==注意：-it很重要，如果没有交互式运行，那么系统认为用完就结束，之后的restart都没用了==



> aws亚马逊云
> ssh 

Docker容器化技术
k8s大规模容器编排技术


MySQL是一个软件  数据库管理系统  MySQL管理的是数据库
Docker是一个软件 容器化管理系统  Docker管理的是容器和镜像

镜像类似于系统盘——镜像里面一定包含一个Linux系统  https://hub.docker.com

```bash
docker pull mysql    
docker pull python
```

容器——容器是靠镜像运行一次后产生的结果

```bash
docker run mysql
```


 将mysql这个镜像运行一次，产生一个容器

镜像是死的，是我们做好的包含了linux及其相关软件的集合体。
容器是活的，是要运行的。
容器可以随时删除，只要保留自己的文件就可以了。

shell：命令行，人和计算机交互用的。
shell是一个统称，常见的shell有sh，bash，zsh等

ubuntu终端的本质就是运行了/bin/bash这个shell程序
苹果终端的本质就是运行了/bin/zsh这个shell程序

一个shell的提示符如果是#，那么意味着现在是root登录的，非常危险。

```bash
docker pull ubuntu:22.04  
docker pull ubuntu   		绝大多数软件会默认下载最新版，有的也会报错

docker images 查看电脑上下载的所有镜像
```

语法：

```bash
docker run 参数 镜像名:版本 命令

docker run --rm -it ubuntu:22.04 bash
```

我们创建了一个容器并且进入了这个系统中的bash这个shell
--rm：这个容器一旦停止/退出/结束就会立刻被删除
-it：以交互方式运行容器，不要让他退到后台去，我们是要进去敲命令的
bash是容器启动后要执行的命令

```bash
docker run --rm ubuntu:22.04 date
```

创建一个容器，让他执行date命令，date命令执行结束容器也就结束了，然后就会删掉这个容器

```bash
docker run --rm ubuntu:22.04 ls /

docker run --rm ubuntu:22.04 bash

docker ps 查看正在运行的容器
docker ps -a  查看所有的容器，包括已经停止的但是没有被删除的
docker start a26  启动一个已经停止的容器 a26要改成自己的，这是容器的编号
docker exec -it a26 bash  # exec表示在一个容器中执行某条命令
```

我们实际上是打算演示一个例子
这个例子表示一个容器曾经停止/结束过，但是我又启动并且再次进入它。

```bash
docker run ubuntu:22.04 bash  # 容器就停止了
docker ps -a                  # 去找到这个容器的编号
docker start 编号              # 启动这个容器
docker exec -it 编号 bash      # 再次进入这个容器
```

下面又是一个同样功能的例子

```bash
sudo docker run -it ubuntu:22.04 bash
sudo docker ps -a
sudo docker start 编号
sudo docker attach 编号	# attach 指进入一个容器类似于 docker exec -it 编号 bash
```

容器在停止之前必须是停止状态

```bash
sudo docker stop 编号	# 停止一个容器的运行
sudo docker rm 编号	# 删除一个已经停止的容器
```

==镜像的操作==

```bash
sudo docker images	# 查看所有镜像
sudo docker rmi 编号		# 删除镜像
```

==数据卷共享==
容器随时可删，只要数据还在。

```bash
mkdir ~/data
sudo docker run -it -v ~/data:/xyz ubuntu:22.04 bash 
```

-v 本机已存在路径:容器中路径   这就是数据卷共享
容器中的路径可以事先不存在，它会自动建立。
数据卷共享就是将容器和本地计算机的文件夹打通。
-v参数可以有好多个，比如

```bash
sudo docker run -it -v ~/data:/xyz -v 路径:路径 ubuntu:22.04 bash 
```



```bash
sudo docker pull mysql:8

sudo docker run --name some-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:8

sudo docker exec -it some-mysql bash
```


此时我们就进入了安装好mysql的容器，此时只需要输入mysql -uroot -p回车输入密码即可

--name表示容器的名字，如果没有指定就随机生成一个
-p参数将容器的端口和本机的端口打通，类似于-v打通文件夹
默认情况下，容器用了数据卷共享，宿主机一把不需要额外操作，机器会找一个默认位置来存放

之后的常见软件都可以通过docker进行安装，比如

```bash
sudo docker run --name some-redis -p 6379:6379 -d redis:7
```

此处的-d表示让这个容器在后台运行
当运行一个容器的时候，镜像并不存在，会现场直接下载镜像。
