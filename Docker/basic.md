# Docker基础



### 安装

[安装教程](https://www.cnblogs.com/myzony/p/9071210.html)



## 帮助命令



```shell
docker version
docker info # 显示docker系统信息 镜像 容器等信息
docker 命令 --help
```



## 镜像命令



```shell
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              18.04               3339fde08fc3        4 weeks ago         63.3MB
tomcat              latest              1b6b1fe7261e        11 months ago       647MB
centos              latest              470671670cac        15 months ago       237MB

# 可选项
-a # 显示所有镜像
-q # 只显示id

# [root@localhost ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10777               [OK]   
mariadb                           MariaDB Server is a high performing open sou…   4058                [OK]   

# 搜索starts数目大于等于10000的
[root@localhost ~]# docker search mysql --filter=stars=10000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   10777               [OK]              

# 下载镜像
docker pull redis # 下载redis默认下载最新版本
# 下载指定版本
docker pull redis:3.2
# 删除镜像
docker rmi -f e868ba51f450 # 删除指定镜像
docker rmi -f e868ba51f450 sefra51f423 739b59b96069 # 删除多个镜像
docker rmi -f $(docker images -aq) # 删除全部
```



## 容器命令



```shell
# 新建并启动
docker run [可选参数] images
# 可选参数
--name="Name"	# 容器名字 tomcat01 tomcat02 用来区分容器
-d				# 以后台方式启动
-p				# 指定端口 -p 8080:8080 主机端口:容器端口 若不写随机指定端口
-it				# 使用交互方式运行，可进入容器查看内容

# 启动并进入容器，主机名就是镜像名字
[root@localhost ~]# docker run -it centos /bin/bash
[root@73731ea96092 /]# 
# 退出容器
exit			# 退出并停止容器
CTRL + P + Q	# 退出不停止容器

# 进入容器
docker exec -it 61725088a12a  /bin/bash	# 启动一个新的终端，可以在里面操作(常用)
docker attach 61725088a12a				# 进入容器正在执行的终端
# 查看容器
docker ps 			# 列出正在运行的容器
docker ps -a		# 列出所有的容器
docker ps -aq		# 列出所有的容器编号
docker ps -a -n=1	# 列出最近的一个容器

# 删除容器
docekr rm 容器id 	# 不能删除正在运行中的容器 -f 强制删除
docekr rm -f $(docekr ps -aq)

# 启动和停止容器
docker start 容器id
docker restart 容器id
docker start 容器id
docker stop 容器id
docker kill 容器id
```



## 其他常用命令



### 查看日志



```shell
docker logs -tf --tail 10 61725088a12a # 实时查看指定容器10条日志后边一直追加
```



### 查看进程信息



```shell
[root@localhost ~]# docker top 61725088a12a
UID                 PID                 PPID                C                   STIME               TTY     
root                3510                3495                0                   23:10               pts/0
```



### 查看镜像源数据



```shell
docker inspect 61725088a12a
```



### 拷贝容器文件到主机



```shell
docker cp 容器id:容器内路径	目的主机路径
```



## 练习



### 运行nginx



```shell
docker pull nginx									# 拉镜像
docker run -d -p 8081:80  --name nginx1 nginx		# 运行容器
curl 127.0.0.1:8081									# 测试
```



### 部署ES



```shell
docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
curl 127.0.0.1:9200
```



# 镜像讲解



## 简介



轻量级的、可执行的独立软件包。所有应用打包docker镜像，直接运行



## 镜像加载原理



UnionFS(联合文件系统)，它支持对文件系统的修改作为一次提交来一层层叠加



## 制作自己镜像



### 提交镜像



```shell
docker commit 容器id		# 提交容器成为一个新的副本
docker commit -m"提交的信息" -a"作者" 容器id 生成的镜像名字:版本
# 例如
docker commit -m"app第一版" -a"tyc" 6aud8292239 xfapp:1.0.0
```



# 容器数据卷



## 简介



容器内数据与外部进行挂载任何一方改变都会引起对方改变，防止删除容器后数据丢失



## 使用数据卷



方式一：直接使用命令进行挂载



```shell
docker run -it -v 主机目录:容器内目录 
# 测试
docker inspect 容器id			# 查看Mounts下内容，判断是否挂载成功
```



### 具名挂载、匿名挂载与指定路径挂载



```shell
-v 容器路径				# 匿名挂载
-v 名字:容器路径		   # 具名挂载
-v 外部路径:容器内路径    # 指定目录挂载
# 具名挂载 
 docker run -d -p8081:80 --name nginx01 -v /etc/nginx nginx
# 查看挂载情况
docker volume ls 	# 查看docker下所有挂载文件
docker inspect  [容器名]  # 查看容器详细信息 Mount下包含挂载信息
```



## 练习



### 安装Mysql



```shell
docker pull mysql:5.7	
# 启动mysql 挂载数据卷 配置root密码
[root@localhost ~]# docker run -d -p 3310:3306 -v /usr/docker/mysql/conf:/etc/mysql/conf.d -v /usr/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name=mysql01 mysql:5.7
# navicat连接测试
# 测试联通后 新建数据库 查看linux映射目录上数据是否存在 删除容器 数据仍然存在
```



# DockerFile



docekrfile是用来构建docker镜像的文件！命令参数脚本



**构建步骤**



1. 编写dockerfile文件
2. docker build 构建成为一个镜像

1. docker run 运行镜像
2. docker push 发布镜像



## 指令



```shell
FROM 		# 基础镜像
MAINTAINER  # 镜像是谁写的 姓名+邮箱
RUN 		# 构建镜像时需要运行的命令
ADD			# 加一些需要的东西 例如：tomcat
WORKDIR		# 镜像工作目录
VOLUMN		# 挂载的目录
EXPOSE		# 暴露端口
CMD			# 指定容器启动时运行的命令,只有最后一个会生效
ENTRYPOINT  # 可以追加命令
COPY		# 将文件拷贝到镜像
ENV			#构建时设置环境变量
```



## 实战



### centos



```shell
# 编写文件
FROM centos
MAINTAINER tyc<1573496757@qq.com>

ENV MYPATH /usr/local

WORKDIR  $MYPATH 

RUN yum -y  install vim
RUN yum -y install net-tools

EXPOSE 8088

CMD echo $MYPATH
CMD "-----end-----"
CMD /bin/bash

# 构建
docker build -f mycentos  -t mycecntos:0.1 .
# 测试

docker history 镜像id   #查看此镜像构建过程
```



### Tomcat构建



准备好Tomcat压缩包和JDK压缩包 readme.txt



编写Dockerfile,vim Dockerfile



```shell
FROM centos
MAINTAINER tyc@1573496757@qq.com

COPY readme.txt /usr/local/readme.txt

ADD JDK名称 /usr/local
ADD tomcat名称 /usr/local

ENV MYPATH /usr/local

WORKDIR  $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0.11
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:CLASSPATH $JAVA_HOME/lib/tools.jar
# 配置tomcat与java类似

ENV PATH $PATH:$JAVA_HOME/bin

EXPOSE 8080

CMD /user/local/tomcat名称/bin/stratup.sh && tail -f /usr/local/tomcat名称/logs/catalina.out
```



# Docker网络



```shell
# 我的linux ip:192.168.101.7
ip addr		#查看信息
# 启动一个tomcat进去查看ip地址
docker exec -it 5783cd9750d9 /bin/bash		#进入容器
# 查看IP ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
24: eth0@if25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 容器外执行
 ping 172.17.0.2 		# 可以ping通
# 容器内执行
ping 192.168.101.7      # 可以ping通
# linux下的其中一个网卡信息 注意与docker中的ip 24:信息
25: vethb13a1f8@if24: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default
    link/ether 4e:88:dc:92:e0:28 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::4c88:dcff:fe92:e028/64 scope link
       valid_lft forever preferred_lft forever
# linux与docker这种一对的虚拟设备接口 evth-pair：一对的虚拟设备接口一段连着协议一段彼此互联
# 再启动一个Tomcat容器 它们也是可以通信的，一个容器不是直接连接到一个容器内部，而是到先外部再由外部到容器内部


# 如何通过名字来访问服务，因为通过Dokcer启动我们无法在之前确定ip

# 方案一 --link
# 新启动tomcat03连接tomcat02
 docker run -it -d -P --name tomcat03 --link tomcat02 tomcat:8.0
# 进入容器后 ping tomcat02通过，但是tomcat02无法通过tomcat03名字ping通
# 查看tomcat03网络配置
 docker exec -it  tomcat03 cat /etc/hosts
 127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.3      tomcat02 5e21178f37fa		# 这里配置里tomcat02
172.17.0.4      0a8fd3b044ca
# 方案二  自定义网络
# 查看
docker network ls
# 启动容器时默认 --net bridge
docker run -d -P --name tomcat01 --net bridge tomcat:8.0
# 创建自定义网络
docker network create --driver bridge --subnet 192.168.0.1/16 --gateway 192.168.0.1 mynet
# 查看自定义网络信息
docker network inspect mynet
# 启动三个容器采用自定义网络
docker run -d  -P --name tomcat-net-01 --net mynet tomcat:8.0
docker run -d  -P --name tomcat-net-02 --net mynet tomcat:8.0
docker run -d  -P --name tomcat-net-03 --net mynet tomcat:8.0
# 测试随便进入一个容器都可以通过名称ping通过
# 再次执行docker network inspect mynet查看信息 多了以下内容
 "Containers": {
            "c9351696e930e1b63aa6cf5df32c47ac0e14e97df0a90987cf776359ac2ca33d": {
                "Name": "tomcat-net-03",
                "EndpointID": "578d28ad6ec60dec0fb463242c73722606ea8fe97a3bac3b8cadc54daabbc893",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "cef366250288e1b6072d2cb0e523cd1060b3926eb2467aaf5a4386c422877e08": {
                "Name": "tomcat-net-01",
                "EndpointID": "a126531c010449cc4be43cebe1166d01a2c36047034d1fbf3aafa3b51dd504ca",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "d5877b422af792c0cc62485e0cde1e4bad7de25888feea93b8d33894a56c9743": {
                "Name": "tomcat-net-02",
                "EndpointID": "7681e047da40c20cf78929e142a4890d1efee073d1bf47216d6dff7e0e150669",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
# 将一个已经启动的容器连接到某个网络
docker network connect mynet tomcat01
```



# Docker Compose



**介绍**



定义、运行多个容器，使用docker-compose.yml文件进行配置，也就是批量容器编排



**下载**



```shell
# 下载
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
# 修改权限
chmod +x /usr/local/bin/docker-compose
# 查看版本
docker-compose -version
```



**使用步骤**



1.定义环境dockerfile,保证在任何地方运行



2.定义服务docker-compose.yml



3.docker-compose up 启动



**docker-compose文件**



```shell
version: "3.9"  # optional since v1.27.0
services:		#service表示一个项目包含一组容器
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```



**练习**



```plain
 mkdir composetest
 cd composetest
```



```shell
# vim app.py
import time 

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```



```shell
# vim requirements.txt
flask
redis
```



```shell
# vim docker-compose.yml
version: "2.2"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```



vim Dockerfile



```shell
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```



测试



```plain
http://192.168.101.7:5000/			# linux ip地址
```



**Dockerfile与docker-compose.yml区别**



​	Dockerfile是用来构建镜像的，若是想使用这个镜像的话还需要使用docker run命令来运行这个镜像，从而生成运行一个容器
docker-compose.yml是用来编排项目的，里面包含使用各种镜像创建的容器服务，使用的镜像可以是网络上的，也可以是根据使用Dockerfile文件来生成的镜像，相当于是把上一步的这个工作给做了