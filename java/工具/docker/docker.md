# docker学习笔记
## 1.什么是docker
### 解决了什么难点
1. 在开发中，经常会遇到本地环境与生产环境不一致，然后开发和运维言语沟通或者武力沟通的情况，相互甩锅，程序员最常说的一句话就是：“为什么在我的电脑上没事”
2. 在需要搭建集群的情况时，如果需要在特定的时区快速扩容机器（机器数量庞大，并非人力可为的情况），并且搭建好环境，事后又能快速的关掉
### 解决有哪些方案
1. 可以编写脚本，进行自动部署，自动扩容；
   1. 但这仍然会遇到不可预期的情况，当编写好自动部署脚本，是为Centos的linux服务器，但如果实际情况下服务器是ubuntu的linux，
   则有可能部署不成功 
   2. 其次就是假如我的java项目需要部署到服务器上，我的java项目只能支持java8，如果服务器上已有java11，则无从下手，
      卸载重装则有可能会影响到服务器上的别的程序，如果不装java8，则无法运行我们的程序，隔离性不强
2. 构建虚拟机镜像文件，在虚拟机里运行创建出虚拟机
    1. 他解决了隔离性和一定的跨平台性，但是一台虚拟机创建出来需要模拟整个操作系统，包括硬盘，内存，网络等一系列的操作系统相关的组件
    会很浪费资源
3. 虚拟容器技术
    1. 他解决了隔离性和跨平台性，他并不像虚拟机一样庞大，硬件驱动相关的与主机共用（主机：正真的电脑，宿机：虚拟出来的），只是环境相关
       的为容器内部所管理，容器和容器之间互不影响，docker是这种技术的一种实现，他可以让开发者配置好DockerFile，构建出镜像或者直接推至仓库 ，
       运维拿到镜像则直接使用少量参数则器启动
### docker的三要素
1. 仓库，类似于git服务器、maven中央仓库、百度云盘（不严谨，但好理解）、等，仓库用于存放镜像，我们可以拉取镜像，用来快速创建容器，[docker官方的仓库地址](https://registry.hub.docker.com/)
2. 镜像，用镜像可以创建出一个或者多个容器，可以认为是一个模板，类似java的类，用类可以创建出对象，比如windows10的安装文件，利用这一个文件可以给无数个电脑安装windows
3. 容器，利用镜像创建出来的真实运行环境，他是一个虚拟出的一个小型的linux，也是最终运行应用程序的东西
## 2.docker常用命令
### docker 系统相关
#### docker info
docker info 可以查看docker的详细信息，包括镜像数，容器数，主机硬件信息等
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker info
Containers: 8
 Running: 6
 Paused: 0
 Stopped: 2
Images: 8
Server Version: 1.13.1
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: journald
Cgroup Driver: systemd
Plugins: 
 Volume: local
 Network: bridge host macvlan null overlay
Swarm: inactive
Runtimes: docker-runc runc
Default Runtime: docker-runc
Init Binary: /usr/libexec/docker/docker-init-current
containerd version:  (expected: aa8187dbd3b7ad67d8e5e3a15115d3eef43a7ed1)
runc version: 66aedde759f33c190954815fb765eedc1d782dd9 (expected: 9df8b306d01f59d3a8029be411de015b7304dd8f)
init version: fec3683b971d9c3ef73f284f176672c44b448662 (expected: 949e6facb77383876aeff8a6944dde66b3089574)
Security Options:
 seccomp
  WARNING: You're not using the default seccomp profile
  Profile: /etc/docker/seccomp.json
Kernel Version: 3.10.0-1127.19.1.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
Number of Docker Hooks: 3
CPUs: 2
Total Memory: 3.561 GiB
Name: iZwz9g1c3fleilt56ermd5Z
ID: LJDY:TL4J:A5QL:NYA7:R25T:FRU2:BNVK:ANCI:GC6F:OZQ5:C4IN:JAK5
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Experimental: false
Insecure Registries:
 127.0.0.0/8
Registry Mirrors:
 https://n81gglan.mirror.aliyuncs.com
Live Restore Enabled: false
Registries: docker.io (secure)
```
#### docker version
docker version 可以查看docker服务端和客户端版本信息
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker version
Client:
 Version:         1.13.1
 API version:     1.26
 Package version: docker-1.13.1-203.git0be3e21.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      0be3e21/1.13.1
 Built:           Thu Nov 12 15:11:46 2020
 OS/Arch:         linux/amd64

Server:
 Version:         1.13.1
 API version:     1.26 (minimum version 1.12)
 Package version: docker-1.13.1-203.git0be3e21.el7.centos.x86_64
 Go version:      go1.10.3
 Git commit:      0be3e21/1.13.1
 Built:           Thu Nov 12 15:11:46 2020
 OS/Arch:         linux/amd64
 Experimental:    false
```
### docker 镜像相关
#### docker images
docker images 可以查看本地的镜像
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
docker.io/wordpress            latest              cfb931188dab        2 months ago        546 MB
docker.io/redis                latest              74d107221092        2 months ago        104 MB
docker.io/mysql                5.7.32              1b12f2e9257b        3 months ago        448 MB
docker.io/elasticsearch        7.9.3               1ab13f928dc8        3 months ago        742 MB
docker.io/nacos/nacos-server   latest              a81222848024        3 months ago        921 MB
docker.io/hello-world          latest              bf756fb1ae65        12 months ago       13.3 kB
docker.io/kibana               7.4.2               230d3ded1abc        15 months ago       1.1 GB
docker.io/nginx                1.10                0346349a1a64        3 years ago         182 MB
```
展示出来的表格参数详解

|   PREOSITORY   |    TAG    |       IMAGE ID       |    CREATED    |   SIZE  |
| -------------- | --------- | -------------------- | ------------- | ------- |
| 表示镜像的仓库源 | 镜像的标签 | 镜像ID（由docker产生） | 镜像创建的时间  | 镜像大小 |

同一个镜像可以有多个TAG，可以认为是镜像的版本，最新版本为latest，如果不指定版本标签，默认为latest
docker images 还有许多参数
> -a: 用于查看全部的镜像对应单词 all
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker images -a
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
docker.io/wordpress            latest              cfb931188dab        2 months ago        546 MB
docker.io/redis                latest              74d107221092        2 months ago        104 MB
docker.io/mysql                5.7.32              1b12f2e9257b        3 months ago        448 MB
docker.io/elasticsearch        7.9.3               1ab13f928dc8        3 months ago        742 MB
docker.io/nacos/nacos-server   latest              a81222848024        3 months ago        921 MB
docker.io/hello-world          latest              bf756fb1ae65        12 months ago       13.3 kB
docker.io/kibana               7.4.2               230d3ded1abc        15 months ago       1.1 GB
docker.io/nginx                1.10                0346349a1a64        3 years ago         182 MB
```
> -q/-qa： -q是只查看image id的结果，-qa则是-a和-q的组合
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker images -q
cfb931188dab
74d107221092
1b12f2e9257b
1ab13f928dc8
a81222848024
bf756fb1ae65
230d3ded1abc
0346349a1a64
```
> --digests：可以查看镜像的说明，使用命令后，展示的表格会多出一个DIGEST列
```shell
[root@iZwz9g1c3fleilt56ermd5Z docker]# docker images --digests
REPOSITORY                     TAG                 DIGEST                                                                    IMAGE ID            CREATED             SIZE
docker.io/wordpress            latest              sha256:92e97d9b3147038e3cc541a224cc951bef597061827e23a208a24c36bff1c1fe   cfb931188dab        2 months ago        546 MB
docker.io/redis                latest              sha256:5b98e32b58cdbf9f6b6f77072c4915d5ebec43912114031f37fa5fa25b032489   74d107221092        2 months ago        104 MB
docker.io/mysql                5.7.32              sha256:4d2b34e99c14edb99cdd95ddad4d9aa7ea3f2c4405ff0c3509a29dc40bcb10ef   1b12f2e9257b        3 months ago        448 MB
docker.io/elasticsearch        7.9.3               sha256:a13cd87cbf139fadbca64972ef2c8777222236887d303e4177c1ab7cff1b52f6   1ab13f928dc8        3 months ago        742 MB
docker.io/nacos/nacos-server   latest              sha256:c2f6fee129ed53381ab6973313f77fe9438b6cb52df23ac026903d9592b81ce1   a81222848024        3 months ago        921 MB
docker.io/hello-world          latest              sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d   bf756fb1ae65        12 months ago       13.3 kB
docker.io/kibana               7.4.2               sha256:355f9c979dc9cdac3ff9a75a817b8b7660575e492bf7dbe796e705168f167efc   230d3ded1abc        15 months ago       1.1 GB
docker.io/nginx                1.10                sha256:6202beb06ea61f44179e02ca965e8e13b961d12640101fca213efbfd145d7575   0346349a1a64        3 years ago         182 MB
```
> --no-trunc：可以查看镜像id更加详细的信息
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker images --no-trunc 
REPOSITORY                     TAG                 IMAGE ID                                                                  CREATED             SIZE
docker.io/wordpress            latest              sha256:cfb931188dab87aa1cb9da7b94068fecdb5d1f7e1372a381a7555b2acf3476f9   2 months ago        546 MB
docker.io/redis                latest              sha256:74d107221092875724ddb06821416295773bee553bbaf8d888ababe9be7b947f   2 months ago        104 MB
docker.io/mysql                5.7.32              sha256:1b12f2e9257be96da5c075f186dc371b18442b0f9281728ac64c9a69c6e0e264   3 months ago        448 MB
docker.io/elasticsearch        7.9.3               sha256:1ab13f928dc8aa958574d060fde595bd7715c1c2d3260446356a6a02d231e168   3 months ago        742 MB
docker.io/nacos/nacos-server   latest              sha256:a81222848024f406b6bef585540847bcfea1a06ec97d64e954835ee7411c53f3   3 months ago        921 MB
docker.io/hello-world          latest              sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b   12 months ago       13.3 kB
docker.io/kibana               7.4.2               sha256:230d3ded1abc1468536e41d80a9cc6a67908358c0e4ebf065c29b8ef0370ba4b   15 months ago       1.1 GB
docker.io/nginx                1.10                sha256:0346349a1a640da9535acfc0f68be9d9b81e85957725ecb76f3b522f4e2f0455   3 years ago         182 MB
```
#### docker search 
docker search 镜像名字， 可以去中央仓库搜索镜像，对应的网站地址[docker镜像仓库](https://registry.hub.docker.com/search?type=image)
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker search tomcat
INDEX       NAME                                    DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/tomcat                        Apache Tomcat is an open source implementa...   2919      [OK]       
docker.io   docker.io/tomee                         Apache TomEE is an all-Apache Java EE cert...   86        [OK]       
docker.io   docker.io/dordoka/tomcat                Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 ba...   55                   [OK]
docker.io   docker.io/bitnami/tomcat                Bitnami Tomcat Docker Image                     37                   [OK]
docker.io   docker.io/kubeguide/tomcat-app          Tomcat image for Chapter 1                      29                   
docker.io   docker.io/consol/tomcat-7.0             Tomcat 7.0.57, 8080, "admin/admin"              17                   [OK]
docker.io   docker.io/cloudesire/tomcat             Tomcat server, 6/7/8                            15                   [OK]
docker.io   docker.io/aallam/tomcat-mysql           Debian, Oracle JDK, Tomcat & MySQL              13                   [OK]
docker.io   docker.io/arm32v7/tomcat                Apache Tomcat is an open source implementa...   10                   
docker.io   docker.io/maluuba/tomcat7-java8         Tomcat7 with java8.                             6                    
docker.io   docker.io/rightctrl/tomcat              CentOS , Oracle Java, tomcat application s...   6                    [OK]
docker.io   docker.io/unidata/tomcat-docker         Security-hardened Tomcat Docker container.      5                    [OK]
docker.io   docker.io/arm64v8/tomcat                Apache Tomcat is an open source implementa...   3                    
docker.io   docker.io/amd64/tomcat                  Apache Tomcat is an open source implementa...   2                    
docker.io   docker.io/cfje/tomcat-resource          Tomcat Concourse Resource                       2                    
docker.io   docker.io/jelastic/tomcat               An image of the Tomcat Java application se...   2                    
docker.io   docker.io/99taxis/tomcat7               Tomcat7                                         1                    [OK]
docker.io   docker.io/camptocamp/tomcat-logback     Docker image for tomcat with logback integ...   1                    [OK]
docker.io   docker.io/chenyufeng/tomcat-centos      tomcat基于centos6的镜像                              1                    [OK]
docker.io   docker.io/oobsri/tomcat8                Testing CI Jobs with different names.           1                    
docker.io   docker.io/picoded/tomcat7               tomcat7 with jre8 and MANAGER_USER / MANAG...   1                    [OK]
docker.io   docker.io/ppc64le/tomcat                Apache Tomcat is an open source implementa...   1                    
docker.io   docker.io/s390x/tomcat                  Apache Tomcat is an open source implementa...   0                    
docker.io   docker.io/secoresearch/tomcat-varnish   Tomcat and Varnish 5.0                          0                    [OK]
docker.io   docker.io/softwareplant/tomcat          Tomcat images for jira-cloud testing            0                    [OK]
```
参数说明
> -s 指定点赞数，只展示超过指定点赞数的数据
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker search -s 30 tomcat
Flag --stars has been deprecated, use --filter=stars=3 instead
INDEX       NAME                       DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/tomcat           Apache Tomcat is an open source implementa...   2919      [OK]       
docker.io   docker.io/tomee            Apache TomEE is an all-Apache Java EE cert...   86        [OK]       
docker.io   docker.io/dordoka/tomcat   Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 ba...   55                   [OK]
docker.io   docker.io/bitnami/tomcat   Bitnami Tomcat Docker Image                     37                   [OK]
```
> --no-trunc 不省略，当字符超过展示数时省略的三个...功能关掉
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker search -s 30 --no-trunc tomcat
Flag --stars has been deprecated, use --filter=stars=3 instead
INDEX       NAME                       DESCRIPTION                                                                                            STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/tomcat           Apache Tomcat is an open source implementation of the Java Servlet and JavaServer Pages technologies   2919      [OK]       
docker.io   docker.io/tomee            Apache TomEE is an all-Apache Java EE certified stack where Apache Tomcat is top dog.                  86        [OK]       
docker.io   docker.io/dordoka/tomcat   Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 based docker container.                                        55                   [OK]
docker.io   docker.io/bitnami/tomcat   Bitnami Tomcat Docker Image                                                                            37                   [OK]
```
> --automated 只列出automated build为ok的镜像
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker search -s 30 --no-trunc --automated tomcat
Flag --stars has been deprecated, use --filter=stars=3 instead
Flag --automated has been deprecated, use --filter=automated=true instead
INDEX       NAME                       DESCRIPTION                                                       STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/dordoka/tomcat   Ubuntu 14.04, Oracle JDK 8 and Tomcat 8 based docker container.   55                   [OK]
docker.io   docker.io/bitnami/tomcat   Bitnami Tomcat Docker Image                                       37                   [OK]
```
#### docker pull
docker pull 镜像名[:tag] 从中央仓库拉取镜像，如果不写tag，等价于docker pull 镜像名:latest
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker pull tomcat
Using default tag: latest
Trying to pull repository docker.io/library/tomcat ... 
latest: Pulling from docker.io/library/tomcat
b9a857cbf04d: Pull complete 
d557ee20540b: Pull complete 
3b9ca4f00c2e: Pull complete 
667fd949ed93: Pull complete 
661d3b55f657: Pull complete 
511ef4338a0b: Pull complete 
a56db448fefe: Pull complete 
00612a99c7dc: Pull complete 
326f9601c512: Pull complete 
c547db74f1e1: Pull complete 
Digest: sha256:94cc18203335e400dbafcd0633f33c53663b1c1012a13bcad58cced9cd9d1305
Status: Downloaded newer image for docker.io/tomcat:latest
```
#### docker rmi
docker rmi 镜像名:tag,没写tag默认是latest，删除某个镜像
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker rmi hello-world
Untagged: hello-world:latest
Untagged: docker.io/hello-world@sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
Deleted: sha256:9c27e219663c25e0f28493790cc0b88bc973ba3b1686355f221c38a36978ac63
```
> -f 强制删除，如果有容器依赖镜像则需要强制删除
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker rmi -f hello-world
Untagged: hello-world:latest
Untagged: docker.io/hello-world@sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```
> 可以批量删除，镜像后面加空格继续追加即可
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker rmi -f hello-world tomcat
Untagged: hello-world:latest
Untagged: docker.io/hello-world@sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
Untagged: tomcat:latest
Untagged: docker.io/tomcat@sha256:94cc18203335e400dbafcd0633f33c53663b1c1012a13bcad58cced9cd9d1305
Deleted: sha256:040bdb29ab375db2a8de090070dcbee19d43f609385a934c3f2b423e515f57bb
Deleted: sha256:03ac467e286d36301dba7daaec530f481074197d22d41bd48a32e5a2e3069573
Deleted: sha256:c56c3d4e36aac2060fdb29c95baf4b4d6916fe9a454751b77b8d89f27dd8dda4
Deleted: sha256:0f3911a87064866cd2aa7fd863afc1e02bdfa4856afa351e871f0e6a48cfd52b
Deleted: sha256:7891c5716382337382836e0af1ea998bf77c1eed9c8e806c7aac5f489952f4ba
Deleted: sha256:56dee2fc4110951264d4b134b303920c633aabbbb0264dc218518b3547320de2
Deleted: sha256:cd253bbc5a1ce457359958f4905b53a3f6f0ac167ba5065f68385600fdbae99e
Deleted: sha256:03b0292dbc6978fe1077f948ac07472a8cac23fb4c3cf92c76f5eb62891e3d5a
Deleted: sha256:19f2a825ed46bbf16a06d9d5185a0ac68196217f302e760abb4cbc3178b14cc1
Deleted: sha256:b4f0436e967b6b9b021d3ed900a5422da6d8fe8c17c51c7d7e4f9c105812f868
Deleted: sha256:4762552ad7d851a9901571428078281985074e5ddb806979dd7ad24748db4ca0
```
> docker rmi -f $(docker ps -qa) ${}可以用命令的结果做占位
### docker 容器相关
#### docker run
docker run 命令可以创建并启动一个容器 参数格式为 docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
> options说明 
  1. --name 容器新名字 为容器指定一个名称
  2. -d 后台运行容器，并返回容器ID，也即启动守护式容器
  3. -i 以交互模式运行容器，通常与-t 同时使用
  4. -t 为容器重新分配一个伪输入终端，通常与-i 同时使用
  5. -P 随机端口映射
  6. -p 指定端口映射，有以下四种模式
        1. ip:hostPort:containerPort
        2. ip:containerPort
        3. hostPort:containerPort
        4. containerPort
> -i和-t 组合使用，运行并且启动虚拟终端
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker run -it --name mycentos01 centos
[root@32c23775d2b1 /]# pwd
```
> -d 守护式进程启动
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker run -d centos
c27c084e44bb8a2d8b23ce62e9e93d3888ff74f909178724b2e101133914bd7b
```
然后docker ps -a 查看发现容器已经退出，原因是docker容器后台运行，就必须有一个前台进程，容器运行的命令如果不是那些一直挂起的命令，就会自动退出
可以使用在容器里执行一个死循环来模拟centos容器不停止
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker run -d centos /bin/sh -c "while true; do echo hello fenqing; sleep 2; done"
f3e5471f302fbf5e9e104146ad9d0b708015a5a22ad984337c265b7ca1ff6c10
```
#### docker ps [OPTIONS]
docker ps 查看现有的容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                            NAMES
32c23775d2b1        centos                "/bin/bash"              27 minutes ago      Up 27 minutes                                                        mycentos01
8b2481b4ac48        redis                 "docker-entrypoint..."   6 weeks ago         Up 2 weeks          0.0.0.0:6379->6379/tcp                           redis
d335af0a7119        nginx:1.10            "nginx -g 'daemon ..."   2 months ago        Up 3 weeks          0.0.0.0:80->80/tcp, 443/tcp                      nginx
253d60c0e3be        kibana:7.4.2          "/usr/local/bin/du..."   2 months ago        Up 2 weeks          0.0.0.0:5601->5601/tcp                           kibana
9ab079d39b8d        elasticsearch:7.9.3   "/tini -- /usr/loc..."   2 months ago        Up 3 weeks          0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elasticsearch
6aa3c6d07701        nacos/nacos-server    "bin/docker-startu..."   2 months ago        Up 3 weeks          0.0.0.0:8848->8848/tcp                           nacos
8d37715ca8a8        mysql:5.7.32          "docker-entrypoint..."   2 months ago        Up 3 weeks          0.0.0.0:3306->3306/tcp, 33060/tcp                mysql
```
> OPTIONS 说明
1. -a 列出当前所有正在运行的容器+历史上运行过的
   ```shell
    [root@iZwz9g1c3fleilt56ermd5Z ~]# docker ps -a
    CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                         PORTS                                            NAMES
    32c23775d2b1        centos                "/bin/bash"              35 minutes ago      Up 35 minutes                                                                   mycentos01
    af3b17c82193        bf756fb1ae65          "/hello"                 About an hour ago   Exited (0) About an hour ago                                                    sad_euler
    8b2481b4ac48        redis                 "docker-entrypoint..."   6 weeks ago         Up 2 weeks                     0.0.0.0:6379->6379/tcp                           redis
    abaece61dd15        wordpress             "docker-entrypoint..."   8 weeks ago         Exited (0) 3 weeks ago                                                          wordpress
    d335af0a7119        nginx:1.10            "nginx -g 'daemon ..."   2 months ago        Up 3 weeks                     0.0.0.0:80->80/tcp, 443/tcp                      nginx
    253d60c0e3be        kibana:7.4.2          "/usr/local/bin/du..."   2 months ago        Up 2 weeks                     0.0.0.0:5601->5601/tcp                           kibana
    9ab079d39b8d        elasticsearch:7.9.3   "/tini -- /usr/loc..."   2 months ago        Up 3 weeks                     0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elasticsearch
    6aa3c6d07701        nacos/nacos-server    "bin/docker-startu..."   2 months ago        Up 3 weeks                     0.0.0.0:8848->8848/tcp                           nacos
    8d37715ca8a8        mysql:5.7.32          "docker-entrypoint..."   2 months ago        Up 3 weeks                     0.0.0.0:3306->3306/tcp, 33060/tcp                mysql
   ```
2. -l 显示最近创建的容器
   ```shell
    [root@iZwz9g1c3fleilt56ermd5Z ~]# docker ps -l
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
    32c23775d2b1        centos              "/bin/bash"         38 minutes ago      Exited (0) 11 seconds ago                       mycentos01
   ```
3. -n 显示最近n个创建的容器
   ```shell
    [root@iZwz9g1c3fleilt56ermd5Z ~]# docker ps -n 5
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                         NAMES
    32c23775d2b1        centos              "/bin/bash"              39 minutes ago      Exited (0) 50 seconds ago                                    mycentos01
    af3b17c82193        bf756fb1ae65        "/hello"                 About an hour ago   Exited (0) About an hour ago                                 sad_euler
    8b2481b4ac48        redis               "docker-entrypoint..."   6 weeks ago         Up 2 weeks                     0.0.0.0:6379->6379/tcp        redis
    abaece61dd15        wordpress           "docker-entrypoint..."   8 weeks ago         Exited (0) 3 weeks ago                                       wordpress
    d335af0a7119        nginx:1.10          "nginx -g 'daemon ..."   2 months ago        Up 3 weeks                     0.0.0.0:80->80/tcp, 443/tcp   nginx
   ```
4. -q 静默模式，只显示容器编号
   ```shell
    [root@iZwz9g1c3fleilt56ermd5Z ~]# docker ps -q
    8b2481b4ac48
    d335af0a7119
    253d60c0e3be
    9ab079d39b8d
    6aa3c6d07701
    8d37715ca8a8
   ```
5. --no-trunc 不截断输出
#### 退出容器的两种方式
1. exit 容器停止退出,在容器终端内输入该命令退出
2. ctrl+P+Q 容器不停止退出，在容器终端内输入按下组合键退出
#### docker start
docker start 容器id或者容器名，可以启动容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker start mycentos01
mycentos01
```
#### docker restart
docker restart 容器id或者容器名,可以重启容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker restart mycentos02
mycentos02
```
#### docker stop 
docker stop 容器id或者容器名，可以停止容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker stop mycentos02
mycentos02
```
#### docker kill 
docker kill 容器id或者容器名，强制停止容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker kill mycentos01
mycentos01
```
#### docker rm
docker rm 删除已停止的容器
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker rm mycentos01
mycentos01
```
> options 说明
1. -f 强制删除容器，会执行先停止再删除流程
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker rm -f  mycentos02
mycentos02
```
2. 一次性删除多个容器
  1. docker rm -f $(docker ps -a -q) 删除所有容器，将$()内的命令结果当做参数,属于shell语法
  2. docker ps -a -q | xargs docker rm 删除所有容器，将前者命令的结果当做参数给到后者命令
#### docker logs 
docker logs 查询容器的日志
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker logs f3e
hello fenqing
hello fenqing
hello fenqing
```
options说明
> -t 及收入时间戳
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker logs -t f3e
2021-01-25T02:06:24.337771000Z hello fenqing
2021-01-25T02:06:26.346211000Z hello fenqing
2021-01-25T02:06:28.348766000Z hello fenqing
```
> -f 跟随最新日志打印
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker logs -f f3e
hello fenqing
hello fenqing
hello fenqing
```
> --tall 数字显示最后多少条
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker logs --tail 1 f3e
hello fenqing
```
#### docker top
docker top 查看docker容器内进程
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker top f3e
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                31088               31070               0                   10:06               ?                   00:00:00            /bin/sh -c while true; do echo hello fenqing; sleep 2; done
root                31394               31088               0                   10:12               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 2
```
#### docker inspect
docker inspect 容器id 查看容器内部细节
```shell
[root@iZwz9g1c3fleilt56ermd5Z ~]# docker inspect f3e
[
    {
        "Id": "f3e5471f302fbf5e9e104146ad9d0b708015a5a22ad984337c265b7ca1ff6c10",
        "Created": "2021-01-25T02:06:24.13609455Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true; do echo hello fenqing; sleep 2; done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 31088,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-01-25T02:06:24.339002895Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/f3e5471f302fbf5e9e104146ad9d0b708015a5a22ad984337c265b7ca1ff6c10/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/f3e5471f302fbf5e9e104146ad9d0b708015a5a22ad984337c265b7ca1ff6c10/hostname",
        "HostsPath": "/var/lib/docker/containers/f3e5471f302fbf5e9e104146ad9d0b708015a5a22ad984337c265b7ca1ff6c10/hosts",
        "LogPath": "",
        "Name": "/cocky_kalam",
        "RestartCount": 0,
        "Driver": "overlay2",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "journald",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "docker-runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DiskQuota": 0,
            "KernelMemory": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": -1,
            "OomKillDisable": false,
            "PidsLimit": 0,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0
        },
        "GraphDriver": {
            "Name": "overlay2",
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/ce04f8aeb99d708b4fa3ea2eb45aaf1b3daa53195d8afc0cbcaf575b73053e97-init/diff:/var/lib/docker/overlay2/9c9e47d354836790de324736040a427c9d171929bb70aaca3baa60e837491777/diff",
                "MergedDir": "/var/lib/docker/overlay2/ce04f8aeb99d708b4fa3ea2eb45aaf1b3daa53195d8afc0cbcaf575b73053e97/merged",
                "UpperDir": "/var/lib/docker/overlay2/ce04f8aeb99d708b4fa3ea2eb45aaf1b3daa53195d8afc0cbcaf575b73053e97/diff",
                "WorkDir": "/var/lib/docker/overlay2/ce04f8aeb99d708b4fa3ea2eb45aaf1b3daa53195d8afc0cbcaf575b73053e97/work"
            }
        },
        "Mounts": [],
        "Config": {
            "Hostname": "f3e5471f302f",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true; do echo hello fenqing; sleep 2; done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "844f87080acf92b538f4948ca6dae4bb4764b356e51ae46199156eeaff9ece71",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/844f87080acf",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "d3ba39bbd5532b1f7c6d2cf21bb107ea3f9ab456e4b5103ee512a2d1fe936d18",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.8",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:08",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "7aaaabad7bc8c86d46db221e582b43b38d2c746e6f1c9b589eae2cbc0e657a26",
                    "EndpointID": "d3ba39bbd5532b1f7c6d2cf21bb107ea3f9ab456e4b5103ee512a2d1fe936d18",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.8",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:08"
                }
            }
        }
    }
]
```