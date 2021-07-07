## docdocker基础

视频地址：https://www.bilibili.com/video/BV1kv411q7Qc?p=4&t=1486

### 1.Docker概念

1.是一个开源的应用容器引擎
2.诞生于2013年初，基于Go语言实现，dotCloud公司出品（后改名为Docker Inc）
3.Docker可以让开发者 **打包他们的应用以及依赖包（环境）到一个轻量级、可移植的容器** 中，然后发布到任何流行的Linux机器上
4.容器是完全使用沙箱机制，相互隔离
5.容器性能开销极低。
6.Docker从17.03版本之后分为CE （CommunityEdition：社区版） 和EE （Enterprise Edition：企业版）
**小结：docker是一种容器技术，解决软件跨环境迁移的问题。**

docker文档：https://docs.docker.com/compose/gettingstarted/

### 2.mac安装docker

1.直接官网下载
2.使用命令
brew cask install docker

### 3.docker架构

1.镜像（Image） ： Docker镜像（Image），就相当于是一个root文件系统。比如官方镜像ubuntu：16.04就包含了完整的一套Ubuntu16.04最小系统的root文件系统。通过镜像可以创建多个容器。
2.容器（Container） ：镜像（Image） 和容器（Container）的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
3.仓库（Repository） ：仓库可看成一个代码控制中心，用来保存镜像。

### 4.配置阿里的镜像加速器

1.进入阿里官网，搜索镜像，点击管理控制台![image-20200220143644647](/Users/yutang/Library/Application Support/typora-user-images/image-20200220143644647.png)2.点击镜像加速器
3.复制加速地址，每个地址是自己特有的
![image-20200220143744239](/Users/yutang/Library/Application Support/typora-user-images/image-20200220143744239.png)

4.进入docker的偏好设置
![image-20200220143937638](/Users/yutang/Library/Application Support/typora-user-images/image-20200220143937638.png)

复制好的镜像地址添加的registry-mirrors列表中，点击应用重启。

### 5.docker与虚拟机的区别

![image-20200220150519084](/Users/yutang/Library/Application Support/typora-user-images/image-20200220150519084.png)

![image-20200222174208321](/Users/yutang/Library/Application Support/typora-user-images/image-20200222174208321.png)

![image-20200222174453311](/Users/yutang/Library/Application Support/typora-user-images/image-20200222174453311.png)

### docker底层原理

docker是一个c-s结构的系统，docker的守护进程运行在主机上，通过socket从客户端访问。

dockerServer接收到dockerClient的指令就会执行这个命令。

执行docker run 容器名 ，如果找不到，就会去远程仓库下载，在run执行。

### docker命令

```shell
docker version
docker info
docker 命令 --help
docker stats # 查看docker使用状态
```

###docker可视化工具

portainer

```shell
# 下载portainer,并启动
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
# 停止
docker stop ed5
# 开启
docker start ed5
```

### 6.docker镜像命令

docker **images** 列出本地的镜像

```shell
yutang@yutang:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              afaec1334369        6 months ago        471MB

# 解释
REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的id
CREATED 镜像的创建时间
SIZE 镜像的大小
```

```shell
# 可选项
-a：列出本地所有的镜像（含中间映像层）
-q：只显示镜像ID
--digests ：显示镜像的摘要信息
--no一trunc ：显示完整的镜像信息
```

docker **search** 镜像名    查找公共的可用镜像

```shell
docker search --filter=stars=30 镜像名
# 列出收藏大于30的
```

docker **pull** image_name[:tag]    从配置的镜像下载image

```shell
docker pull mysql:5.7 # 下载5.7版本的mysql
```

docker **push** image_name   发布docker镜像docker **commit**  提交，创建个新镜像
docker **rmi** [-f] image_name/ID   删除指定的镜像,-f强制删除
docker rmi -f images1 images2 删除多个
docker rmi -f $(docker images -aq)  全部删除镜像

```shell
$( )与｀｀
# 获取执行结果，再重组成新的命令行
```

docker **history** 镜像id  查看镜像的构建过程

### 7.docker容器命令

docker **run** [optiond] image [command] [args] 运行xxx的容器

```shell
# 启动并进入容器
docker run -it image /bin/bash
```

```shell
OPTIONS说明（常用） ：
	--name="容器新名字"：为容器指定一个名称,用来区分容器；
	-d：后台运行容器,不进入容器，并返回容器ID，也即启动守护式容器；
	-i： 以交互模式运行容器，进入容器，通常与-t同时使用；
	-t： 为容器重新分配一个伪输入终端，通常与-i同时使用；
	-P：大p，随机指定端口；
 	-p：指定暴露的端口，有以下四种格式
      ip：hostPort：containerPort
      ip：containerPort
      主机端口hostPort：容器端口containerPort # 常用
      containerPor
```

docker **ps**   列出docker当前所有正在运行的容器 

```shell
docker ps -a # 列出所有运行过得容器
docker ps -l # 查看历史运行的容器
docker ps -lq # 返回历史运行容器的id
-n=1 #显示1条最近创建的容器
```

**exit** 退出停止容器（返回到宿主机）
**ctrl+P+Q** 容器退出不停止

docker **kill** 容器ID   杀掉正在运行的容器
docker **stop** 容器名/容器ID      终止运行的容器
docker **start** ID 启动停止的容器

docker **rm** ID 删除已停止的容器
docker rm -f $(docker ps -aq)  强制全部删除容器
docker ps -aq | xargs docker rm  全部删除容器

docker **logs** -t -f --tail 数字 ID 查看docker日志

```shell
-t 是加入时间戳
-f 跟随最新的日志打印
--tail 数字 显示多少条 # 不加数字显示全部日志
```

docker **top** ID 查看docker运行的进程
docker **inspect** ID 查看容器内部的细节

```shell
"Id": "3de94f9e52674cc0a57fc1ff52d9f8c50ceaefd2ac50b93e0a5e9155f329519e", 容器id
"Created": "2020-02-23T08:09:16.243226382Z", 创建时间
"Path": "docker-entrypoint.sh",
"Args": [
	"mysqld"
],
"State": { 状态
  "Status": "exited",
  "Running": false,
  "Paused": false,
  "Restarting": false,
  "OOMKilled": false,
  "Dead": false,
  "Pid": 0,
  "ExitCode": 137,
  "Error": "",
  "StartedAt": "2020-09-16T02:59:28.5667215Z",
  "FinishedAt": "2020-09-16T03:00:23.4477325Z"
},
"Image": "sha256:afaec1334369d4bb6fe566f3f3d4b075ed4b74a6fcba61f9740aee4715888b21", 镜像
"Mounts": [ 挂载信息
            {
                "Type": "bind",
                "Source": "/Users/yutang/mysql/logs", 主机地址
                "Destination": "/logs", docker容器内地址
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/Users/yutang/mysql/data",
                "Destination": "/var/lib/mysql",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            },
            {
                "Type": "bind",
                "Source": "/Users/yutang/mysql/conf.d",
                "Destination": "/etc/mysql/conf.d",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
```

docker attach ID 进入未关闭正在执行的容器，不会启动新的进程
docker **exec** -it ID **/bin/bash** 在宿主机操作容器 

```shell
docker exec -it ID ls -l /tmp
docker exec -it ID /bin/bash 进入容器
# 让命令行支持中文
docker exec -it 3de94f9e5267 env LANG=C.UTF-8 /bin/bash
```

docker **cp** ID:/路径/文件名 主机路径  从容器拷贝数据到主机，容器启动或停止都可以
docker **commit** -m="提交信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名版本名]

### 8.docker容器的数据卷

**数据卷：**
1.数据卷是宿主机中的一个目录或文件
2.当容器目录和数据卷目录绑定后，**双方**的修改都会**立即自动**同步，容器停止也会进行同步
3.一个数据卷可以被多个容器同时挂载
4.一个容器也可以被挂载多个数据卷
![image-20200221000329699](/Users/yutang/Library/Application Support/typora-user-images/image-20200221000329699.png)

**数据卷作用：**
1.容器数据**持久化**，防止删除容器数据也会跟着删除
2.外部机器和容器间接通信
3.容器之间数据交换 
4.可以在容器外部提供修改容器内的配置

**配置数据卷：**
docker run -it  **-v 宿主机目录:容器目录** image_name

```shell
docker run -it -v /home/test:/home centos
# 匿名挂载 只指定容器内的目录
docker run -it --name nginx01 -v /etc/nginx/ nginx 
# 具名挂载 具名:目录
docker run -it --name nginx01 -v juming:/etc/nginx/ nginx 
# 查看挂载的数据卷
docker volume ls
# 查看没有指定宿主机挂载目录的数据卷 默认挂载路径是
docker volume inspect juming
[
    {
        "CreatedAt": "2020-02-23T08:03:39Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming/_data", 挂载路径
        "Name": "9ff2a8af31103e77c073c90c0387af88820e1579b472daa0971704cbb46da535",
        "Options": null,
        "Scope": "local"
    }
]
```

1.目录必须是绝对路径
2.如果目录不存在，会自动创建
3.可以挂载多个数据卷

**数据卷容器：**
多容器进行数据交换
1.多个容器挂载同一个数据卷
2.数据卷容器
![image-20200221002259896](/Users/yutang/Library/Application Support/typora-user-images/image-20200221002259896.png)

**配置数据卷容器：**
1.创建启动c3数据卷容器
docker run -it --name=容器名 **-v /volume** 镜像名:tag /bin/bash

```shell
# 没有指定数据卷目录时 会自动分配一个宿主机的数据卷目录
docker run -it --name=c3 -v /home/data centos:7 /bin/bash
```

2.创建启动c1 c2容器，使用--volumes -from参数设置数据卷
docker run -it --name=c1 **--volumes-from** c3 centos: 7 /bin/bash
docker run -it --name=c2 **--volumes-from** c3 centos: 7 /bin/bash

### 9.部署mysql

在~ 目录下创建mysql目录用于存储mysql数据信息

```shell
mkdir ~/mysql
cd ~/mysql
```

```shell
docker run -id \
-p 3306:3306 \
--name=c_mysql \
-v $PWD/conf.d:/etc/mysql/conf.d \
-v $PWD/logs:/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \ # -e 配置环境
mysql:5.6
```

连接mysql

```shell
docker ps -a
docker start 3de
docker exec -it 3de env LANG=C.UTF-8 /bin/bash
root@3de94f9e5267:/# mysql -u root -p
```

### 10.Dockerfile

dockerfile是用来构建docker镜像的文件

**Docker镜像原理**
1.Docker镜像是由特殊的文件系统叠加而成，一层一层的文件系统构成，称为联合文件系统
2.最底端是bootfs内核,并使用宿主机的bootfs
3.第二层是root文件系统rootfs,称为base image
4.然后再往上可以叠加其他的镜像文件
5.联合文件系统(Union File System)技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统。
6.一个镜像可以放在另一个镜像的上面。位于下面的镜像称为父镜像，最底部的镜像成为基础镜像。
7.当从一个镜像启动容器时，Docker会在最顶层加载一个读写文件系统作为容器。
![image-20200221215328109](/Users/yutang/Library/Application Support/typora-user-images/image-20200221215328109.png)bootfs是内核 rootfs是系统

1. Docker 镜像本质是什么?
●是一个分层文件系统
2. Docker 中一个centos镜像为什么只有200MB,而一个centos操作系统的iso文件要几个个G?
●Centos的iso镜像文件包含 bootfs和rootfs,而docker的centos镜像复用操作系统的bootfs, 只有rootfs和其他镜像层
3. Docker 中一个tomcat镜像为什么有500MB,而一个tomcat安装包只有70多MB?
●由于docker中镜像是分层的，tomcat虽然只有70多MB,但他需要依赖于父镜像和基础镜像,所有整个对外暴露的tomcat镜像大小500多MB

**commit制作镜像**

```shell
docker commit 容器id 镜像名:版本号  制作镜像
docker save -o 压缩文件名称 镜像名:版本号   压缩镜像文件
docker load -i 压缩文件名称   解压缩
```

![image-20200221220550500](/Users/yutang/Library/Application Support/typora-user-images/image-20200221220550500.png)

**注：容器创建的镜像中的挂载数据卷是不在镜像中生成的。**

**Dockerfile概念**
●Dockerfile是一个文本文件
●包含了一条条的指令
●每一条指令构建一层,基于基础镜像,最终构建出一个新的镜像
●对于开发人员:可以为开发团队提供一个完全一致的开发环境
●对于测试人员:可以直接拿开发时所构建的镜像或者通过Dockerfile文件构建一个新的镜像开始工作了
● 对于运维人员:在部署时，可以实现应用的无缝移植

**Dockerfile关键字**

```shell
FROM 					# 基础镜像 
MAINTAINER 		# 作者信息 姓名+邮箱
RUN           # 镜像构建时需要运行的命令
ADD						# 添加的压缩包、文件会自动解压
WORKDIR				# 工作目录
VOLUME        # 挂载目录 ["xx"， ”xx“]
EXPOSE 				# 暴露端口
CMD						# 指定容器启动时运行的命令 ["xx"， ”xx“] 分行写会被覆盖
ENTRPOINT			# 类似CMD，指定容器启动时运行的命令，可以docker run 后追加命令执行
ONBUILD				# 当构架你一个被继承的Dockerfile 这个时候就会运行onbuild的指令
COPY 					# 类似ADD 将文件拷贝到镜像中
ENV 					# 构建的时候设置环境变量
```

![image-20200221223735349](/Users/yutang/Library/Application Support/typora-user-images/image-20200221223735349.png)

![image-20200221224125700](/Users/yutang/Library/Application Support/typora-user-images/image-20200221224125700.png)

**Dockerfile制作镜像**
![image-20200221225236414](/Users/yutang/Library/Application Support/typora-user-images/image-20200221225236414.png)

```shell
mkdir dockerfile
cd dockerfile
vim centos_dockerfile
```

```shell
# 1.编写dockerfile
FROM centos:7 # 指定镜像 有直接使用，没有的话获取下载
MAINTAINER tangyu <17688763124@163.com> # 作者信息

COPY readme.txt /usr/local/readme.txt # 拷贝文件到容器路径

ADD xxx.tar.gz /usr/local # 添加压缩包，会自动解压

RUN yum install -y vim
ENV MYPATH /usr/local
WORKDIR MYPATH

EXPOSE 80
CMD /bin/bash
```

```shell
# 2.构建my_centos镜像  my_centos构建的镜像名
docker build -f ./centos_dockerfile -t my_centos .
```

docker安装python

```shell
# 基于镜像基础
FROM python:3.7
  
# 设置代码文件夹工作目录 /app
WORKDIR /app
  
# 复制当前代码文件到容器中 /app
ADD . /app
  
# 安装所需的包
RUN pip install -r requirements.txt
  
# Run app.py when the container launches
CMD ["python", "app.py"]
```

### 11.docker服务编排

**服务编排：**按照一定的业务规则**批量管理**容器，可以一键启动停止所有服务
微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停，维护的工作量会很大。
1.要从Dockerfile build image或者去dockerhub拉取image 
2.要创建多个container容器
3.要管理这些container (启动停止删除)

**Docker Compose**
Docker Compose是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动和停止。
使用步骤:
1.利用Dockerfile定义运行环境镜像
2.使用**docker-compose.yml**定义组成应用的各服务
3.运行**docker-compose up**启动应用
4.查看docker-compose日志

```shell
docker-compose logs -f
```

**安装docker compose**

```shell
# Compose目前已经完全支持Linux、Mac oS和windows, 在我们安装Compose之前，需要先安装Docker。下面我们以编译好的二进制包方式安装在Linux系统中。
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/1oca1/bin/docker-compose
#设置文件可执行权限
chmod +x /usr/loca1/bin/docker-compose
#查看版本信息
docker-compose -version
```

**卸载docker compose**

```shell
rm /usr/local/bin/docker-compose
```

**使用docker compose 编排**
1.创建docker-compose目录

```shell
mkdir ~/docker-compose
cd ~/docker-compose
```

2.编写Dockerfile，打包为镜像。在docker_compose.yml中build执行

3.编写docker_compose.yml文件 vim

```shell
vim docker_compose.yml # 文件名后缀必须是yml
```

```shell
version:'3'
services: # 容器，应用
	nginx: # 自定义取名字
		image: nginx # 镜像名称
		ports:
			- 80:80
		links:
			- app
		volumes: # 数据卷
			- ./nginx/conf.d:/etc/nginx/conf.d
	app:
		image: app
		expose:
			- "8080"
```

4.启动容器

```shell
docker-compose up
# 后台启动
docker-compose up -d
# 重新部署打包
docker-compose up --build # 重新构建
```

5.停止容器

```shell
docker-compose down 或者 Ctrl+c
```

#### 编写docker_compose.yml

官网地址：https://docs.docker.com/compose/compose-file/查看如何编写

```json
# 3层

version: "3.8" # 版本
services: # 服务
	服务1：web
  	# 服务配置
    image: redis:alpine
    build . # 找到当前目录下的dockerfile去build
		network
    .... 
  服务2：redis
    ...
	服务3：redis
    ...
# 其他配置  网络/卷、全局配置
volumes：
network：
configs：
```

```json
version: "3.8"
services:

  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        max_replicas_per_node: 1
        constraints:
          - "node.role==manager"
```

**depends_on**

某个服务要依赖其他服务启动，先启动对应的服务，在启动该服务

示例：

```json
# 会先启动db和redis再启动web
version: "3.8"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

**网络规则**

通过docker-compose up启动的项目，会创建一个网络，是在同一个网络下，可以通过域名访问(直接通过redis或者nginx连接)

```shell
docker network ls
docker network inspect id
```

![image-20200924224237122](/Users/yutang/Library/Application Support/typora-user-images/image-20200924224237122.png)

#### 博客案例：

官网：https://docs.docker.com/compose/wordpress/

1.创建目录

```shell
cd my_wordpress/
```

2.编写docker_compose.yml文件

```json
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always # 总是重启
     environment: # 环境配置
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports: # 暴露端口
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

3.启动

```shel
docker-compose up
```

案例：

es的docker_compose.yml，es的版本要与kibana对应为6.5.4

/Users/yutang/docker_es

```json
version: "3.1"
	services:
		elasticsearch:
			image: daocloud.io/library/elasticsearch:6.5.4
			restart: always
			container_name: elasticsearch
			ports:
				- 9200:9200
		kibana:
			image: daocloud.io/library/kibana:6.5.4
			restart: always
			container_name: kibana
			ports:
				- 5601:5601
			environment:
				- elasticsearch_url=http://192.168.0.103:9200 # 本机ip
			depends_on:
 				- elasticsearch
```

### 12.docker私有仓库

**私有仓库**
Docker官方的Docker hub (https://hub.docker.com) 是一个用于管理公共镜像的仓库，我们可以从上面拉取镜像到本地，也可以把我们自己的镜像推送上去。但是，有时候我们的服务器无法访问互联网，或者你不希望将自己的镜像放到公网当中，那么我们就需要搭建自己的私有仓库来存储和管理自己的镜像。

**私有仓库搭建**

```shell
# 1、拉取私有仓库镜像
docker pull registry
# 2、启动私有仓库容器
docker run -id --name=registry -p 5000:5000 registry
# 3、打开浏览器输入地址http://私有仓库服务器ip:5000/v2/_catalog,看到{"repositories":[]} 表示私有仓库搭建成功
# 4、修改daemon.json 
vim /etc/docker/daemon.json
#在上述文件中添加一个key,保存退出。此步用于让docker信任私有仓库地址;注意将私有仓库服务器ip修改为自己私有仓库服务器真实ip 
{"insecure-registries": ["私有仓库服务器ip:5000"]}
# 5、重启docker服务
systemct1 restart docker
docker start registryshell
```

**将镜像上传至私有仓库**

```shell
# 1、标记镜像为私有仓库的镜像
docker tag centos:7 私有仓库服务器IP:5000/centos:7
# 2、上传标记的镜像
docker push 私有仓库服务器IP:5000/centos:7
```

**私有仓库拉取镜像**

```shell
# 拉取镜像
docker pull 私有仓库服务器IP:5000/centos:7
```

### 13.docker网络

容器之间可以ping通，通过宿主机间接连接其他容器

![image-20200921233647670](/Users/yutang/Library/Application Support/typora-user-images/image-20200921233647670.png)

```shell
docker network ls
docker network inspect id
```

#### --link

可以在容器内部直接通过容器名ping通对应的容器，而不用ping ip，本质就是在hosts中配置添加了一个 172.18.0.3 tomcat02 id 的映射

```shell
docker run -it --name c1 --link c2 镜像名
docker exec -it c1 ping c2
# 但是反向不可以ping通
```

#### 自定义网路

实现容器互联

查看所有的docker网络

```shell
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
d79e2293281c        bridge              bridge              local
aabd0e0d336f        host                host                local
bf135fbf9b69        none                null                local
# 查看网络信息
docker network inspect 网络id
```

##### 网络模式

bridge：docker默认是桥接 ，容器a通过docker0去连接访问容器b

none：不配置网络

host：和宿主机共享网络

container：容器内网络连通（用的少，局限性比较大）

测试：

```shell
# 默认参数就是bridge
docker run -it --name c1 --net bridge 镜像名
# 自定义网络              网络模式         子网掩码                  网关        网络名称
docker network create --driver bridge --subnet 192.168.0.1/24 --gateway 192.168.0.1 mynet
# 查看
docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
d79e2293281c        bridge              bridge              local
aabd0e0d336f        host                host                local
a14f66afbf94        mynet               bridge              local
bf135fbf9b69        none                null                local
# 使用自定义的网路
docker run -it --name c1 --net mynet 镜像名
```

自定义网络后可以直接使用容器名ping通其他容器

```shell
docker exec -it c1 ping c2
```

#### 网络互通

![image-20200922003523987](/Users/yutang/Library/Application Support/typora-user-images/image-20200922003523987.png)

docker network connect 实现网络连通

```shell
# 打通tomcat-01和mynet
docker network connect mynet tomcat-01
# 测试
docker exec -it tomcat-01 ping tomcat-net-01 
```

要实现**跨网络连通**容器就要使用connect连通，要先创建自定义的网络模式，自己创建的网络模式会修复

容器互联：

1.--link

2.自定义网络

3.connect

### 14.Docker Swarm

官网地址：https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/

Docker Swarm就是docker的集群，服务器机器比较少建议用swarm，多的话就用k8s
swarm就是集群的管理和编号，docker可以初始化一个swarm集群，其他节点可以加入（管理、工作）

Docker Engine 1.12 introduces swarm mode that enables you to create a cluster of one or more Docker Engines called a swarm. A swarm consists of one or more nodes: physical or virtual machines running Docker Engine 1.12 or later in swarm mode.

There are two types of nodes: [**managers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#manager-nodes) and [**workers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#worker-nodes).

两个节点：管理节点和工作节点

![Swarm mode cluster](https://docs.docker.com/engine/swarm/images/swarm-diagram.png)

If you haven’t already, read through the [swarm mode overview](https://docs.docker.com/engine/swarm/) and [key concepts](https://docs.docker.com/engine/swarm/key-concepts/).

操作的命令要都在manangers上，worker节点上不能操作swarm命令

####常用命令

```shell
Commands:
  ca          Display and rotate the root CA
  init        Initialize a swarm
  join        Join a swarm as a node and/or manager
  join-token  Manage join tokens
  leave       Leave the swarm
  unlock      Unlock swarm
  unlock-key  Manage the unlock key
  update      Update the swarm
```

```shell
# 查看节点信息
docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
x144ehpc7ib5n91tnbi92lj4n *   docker-desktop      Ready               Active              Leader              19.03.5
```

1.init

初始化一个swarm节点

--advertise-addr 添加对外连接的地址，地址分为私网和公网

```shell
# 在docker1服务器执行
docker swarm init --advertise-addr 192.168.0.106
```

2.join

添加节点

docker swarm join ... 加入一个节点（管理或者工作）

docker swarm join-token manager 获取管理节点令牌

docker swarm join-token worker 获取工作节点令牌

```shell
# 在docker2服务器执行
docker swarm join --token 节点令牌 192.168.0.106:端口
```

3.leave

离开集群，要稍等一会

```shell
docker swarm leave
```

#### Raft 一致性算法

双主双从：假设一个节点挂了，其他节点是否可用

raft协议：保证大多数管理节点存活才可以用，只要>1，集群至少大于3台！

​	两台管理节点挂了一台，就不能是用了；但三台管理节点挂了一台，还可以正常使用。

#### 弹性、扩缩容

集群：高可用，web-> redis(3台，分布在不同的机器上)

docker service 服务命令 

```shell
Commands:
  create      Create a new service 
  inspect     Display detailed information on one or more services
  logs        Fetch the logs of a service or task
  ls          List services
  ps          List the tasks of one or more services
  rm          Remove one or more services
  rollback    Revert changes to a service's configuration
  scale       Scale one or multiple replicated services
  update      Update a service
```

灰度发布：金丝雀发布

升级不影响服务使用

**docker service create 启动服务**

类似docker run，只不过是run是容器启动，单机的
service create 是服务，有动态扩缩容功能，可以平滑升级、灰度发布

```shell
docker service create -p 8888:80 --name my-nginx nginx
```

docker service ps my-nginx 查看服务运行状态

docker service ls 查看服务

```shell
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
 																														1/1      # 副本数
```

docker service inspect my-nginx 查看服务细节

注：在docker1上docker service create启动的服务会随机启动在任意一个主节点上

**docker service update 扩缩容服务** 

```shell
# 动态扩缩容
docker service update --replicas 3 my-nginx
# 回到一个节点 其他节点服务会停止
docker service update --replicas 1 my-nginx
```

可以实现动态的添加服务容器，3可以是任意数字，会在集群每个节点中包括工作节点去扩充启动服务容器

服务，集群中任意节点都可以访问，服务有多个副本动态扩缩容实现高可用

**docker service scale 扩缩容服务** 

```shell
# 动态扩缩容 跟update --replicas一样
docker service scale my-nginx=5
```

swarm网络

ingress

overlay：使集群编程一个成体，容器之前网络可以互通

### 15.docker小结

1.docker run 启动镜像

2.Dockerfile 构建镜像，服务打包

3.docker-compose 启动项目（编排、多个微服务/环境），告别run启动，docker-compose up启动项目

4.docker网络