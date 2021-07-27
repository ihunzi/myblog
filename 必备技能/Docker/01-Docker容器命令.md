# 容器命令
```
docker run 镜像id 新建容器并启动

docker ps 列出所有运行的容器 docker container list

docker rm 容器id 删除指定容器

docker start 容器id #启动容器

docker restart 容器id #重启容器

docker stop 容器id #停止当前正在运行的容器

docker kill 容器id #强制停止当前容器
```
>说明：我们有了镜像才可以创建容器，Linux，下载centos镜像来学习
docker pull centos

## 新建容器并启动

- docker run

```
docker run [可选参数] [imageName] | docker container run [可选参数] [imageName] 
#参书说明
--name="Name"		容器名字 tomcat01 tomcat02 用来区分容器
-d      后台方式运行
-it     使用交互方式运行，进入容器查看内容
-p      指定容器的端口 -p 8080(宿主机):8080(容器)
		-p ip:主机端口:容器端口(主机端口映射到容器端口)
		-p 主机端口:容器端口(常用)
		-p 容器端口
		容器端口
-P(大写) 				随机指定端口

# 测试、启动并进入容器(事先没有安装centos镜像)
[root@VM-8-17-centos /]# docker run -it centos /bin/bash
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
7a0437f04f83: Pull complete 
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for centos:latest

[root@fdae7762b280 /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var dev  home  lib64  media opt  root  sbin  sys  usr
[root@fdae7762b280 /]# exit #从容器退回主机（容器停止并退出）
exit

# 先安装centos，再进入
root@hecs-x-medium-2-linux-20200620162454:~# docker pull centos
Using default tag: latest
latest: Pulling from library/centos
7a0437f04f83: Pull complete 
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest

root@hecs-x-medium-2-linux-20200620162454:~# docker run -it centos /bin/bash

[root@1fa948a2ac2e /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

```
## 列出所有运行的容器

- docker ps

```
#docker ps命令 #列出当前正在运行的容器
-a 列出当前正在运行的容器+历史运行过的容器
-n=? #显示最近创建过的容器
-q #只显示容器的编号

Options:
  -a, --all             Show all containers (default shows just running)
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print containers using a Go template
  -n, --last int        Show n last created containers (includes all states) (default -1)
  -l, --latest          Show the latest created container (includes all states)
      --no-trunc        Don't truncate output
  -q, --quiet           Only display container IDs
  -s, --size            Display total file sizes
  
[root@VM-8-17-centos /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@VM-8-17-centos /]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
fdae7762b280   centos         "/bin/bash"   6 minutes ago   Exited (0) 2 minutes ago             busy_chaum
c1f0326decec   d1165f221234   "/hello"      2 hours ago     Exited (0) 2 hours ago               crazy_nightingale
[root@VM-8-17-centos /]# docker ps -aq
fdae7762b280
c1f0326decec

```
## 退出容器
```
exit #容器直接退出并停止
ctrl +P +Q #容器不停止退出
```
## 删除容器
```
docker rm 容器id                  #删除指定的容器（不在运行）
docker rm -f 容器id               #强制删除指定的容器（在运行）
docker rm -f $(docker ps -aq)     #删除全部的容器
docker ps -a -qlxargs -docker rm  #删除全部的容器
```
## 容器启停
```
docker start 容器id	#启动容器
docker restart 容器id	#重启容器
docker stop 容器id	#停止当前正在运行的容器
docker kill 容器id	#强制停止当前容器

```