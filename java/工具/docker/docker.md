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
### docker info
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
### docker version
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
### docker images
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
### docker search 
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
### docker pull
docker pull 镜像名[:tag] 从中央仓库拉取镜像，如果不写tag，等价于docker pull 镜像名:latest