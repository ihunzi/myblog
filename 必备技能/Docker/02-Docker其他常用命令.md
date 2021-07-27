# 其他常用命令

## 后台启动命令
```
# docker run -d 镜像名
[root@VM-8-17-centos ~]# docker run -d centos
c58156be129ed181d372d7bfe333b0f6e538cb2fb85f1fdc7327ccb6a4651640

[root@VM-8-17-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# 问题docker ps. 发现centos 停止了
# 常见的坑，docker容器使用后台运行，就必须要有要一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```
## 查看日志
```
[root@VM-8-17-centos ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output #跟踪日志输出
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m
                       for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps #列出时间戳
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes) #在时间戳（例如2013-01-02T13：23：37）或相对（例如42m之前）显示日志，持续42分钟
                       
[root@VM-8-17-centos ~]# docker run -d centos /bin/sh -c  "while true;do echo maxiaoyu;sleep 1;done"
f0f20d7d9d271e7b2edc179d3bc278ba6ccc52073322ff318a20f577331da7ce

docker logs -tf --tail 10 容器id

#显示日志
-tf		      #显示日志信息（一直更新）
--tail number         #需要显示日志条数
docker logs -t --tail n 容器id    #查看n行日志
docker logs -ft 容器id            #跟着日志

```
## 查看容器中进程信息
```
# 命令 docker top 容器id
[root@VM-8-17-centos ~]# docker top f0f20d7d9d27
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                29894               29874               0                   13:41               ?                   00:00:00            /bin/sh -c while true;do echo maxiaoyu;sleep 1;done
root                32207               29894               0                   13:50               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1


```
## 查看镜像的元数据
```
# 命令
docker inspect 容器id

#测试
[root@VM-8-17-centos ~]# docker inspect f0f20d7d9d27
```
## 进入当前正在运行的容器
```
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

方式一
# 命令 
docker exec -it 容器id bash

#测试
[root@VM-8-17-centos ~]# docker exec -it f0f20d7d9d27 /bin/bash
[root@f0f20d7d9d27 /]# 

方式二
docker attach 容器id
#测试
docker attach f0f20d7d9d27
maxiaoyu
maxiaoyu
...
...

区别
#docker exec #进入当前容器后开启一个新的终端，可以在里面操作。（常用）
#docker attach # 进入容器正在执行的终端，不会启动新的进程

```
## 从容器内拷贝到主机上
```
docker cp 容器id:容器内路径 主机目的路径

# 运行centos容器
root@hecs-x-medium-2-linux-20200620162454:~# docker run -it centos /bin/bash
[root@af135c0bfc3a /]# root@hecs-x-medium-2-linux-20200620162454:~# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
af135c0bfc3a   centos    "/bin/bash"              35 seconds ago   Up 34 seconds                                                          magical_euclid
0e86df91679b   mysql     "docker-entrypoint.s…"   4 weeks ago      Up 3 weeks      0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_of_hunzi

# 查看本地home目录下的文件
root@hecs-x-medium-2-linux-20200620162454:~# cd /home/
root@hecs-x-medium-2-linux-20200620162454:/home# ls
djangoProject

# 进入容器内部
root@hecs-x-medium-2-linux-20200620162454:/home# docker attach af135c0bfc3a
[root@af135c0bfc3a /]# cd /home/
[root@af135c0bfc3a home]# ls

# 在容器内部的home目录下新建一个test.txt
[root@af135c0bfc3a home]# touch test.txt
[root@af135c0bfc3a home]# ls
test.txt
[root@af135c0bfc3a home]# exit
exit
root@hecs-x-medium-2-linux-20200620162454:/home# docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED             STATUS                         PORTS                                                  NAMES
af135c0bfc3a   centos        "/bin/bash"              2 minutes ago       Exited (0) 10 seconds ago                                                             magical_euclid
6fa8b3f9d24a   centos        "/bin/bash"              28 minutes ago      Exited (0) 27 minutes ago                                                             vibrant_maxwell
1fa948a2ac2e   centos        "/bin/bash"              49 minutes ago      Exited (130) 35 minutes ago                                                           wizardly_chaum
e0673722763d   hello-world   "/hello"                 About an hour ago   Exited (0) About an hour ago                                                          jolly_taussig
0e86df91679b   mysql         "docker-entrypoint.s…"   4 weeks ago         Up 3 weeks                     0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql_of_hunzi

# 拷贝
root@hecs-x-medium-2-linux-20200620162454:/home# docker cp af135c0bfc3a:/home/test.txt /home
root@hecs-x-medium-2-linux-20200620162454:/home# ls
djangoProject  test.txt
root@hecs-x-medium-2-linux-20200620162454:/home# 

```

## 总结
```
attach      Attach local standard input, output, and error streams to a running container #当前shell下 attach连接指定运行的镜像
build       Build an image from a Dockerfile # 通过Dockerfile定制镜像
commit      Create a new image from a container's changes #提交当前容器为新的镜像
cp          Copy files/folders between a container and the local filesystem #从容器中拷贝指定文件或目录到宿主机中
create      Create a new container #创建一个新的容器，同run，但不启动容器
diff        Inspect changes to files or directories on a container's filesystem #查看docker容器的变化
events      Get real time events from the server # 从docker服务获取容器实时事件
exec        Run a command in a running container # 在运行中的容器上运行命令
export      Export a container's filesystem as a tar archive #导出容器文件系统作为一个tar归档文件[对应import]
history     Show the history of an image # 展示一个镜像形成历史
images      List images #列出系统当前的镜像
import      Import the contents from a tarball to create a filesystem image #从tar包中导入内容创建一个文件系统镜像[对应export]
info        Display system-wide information # 显示全系统信息
inspect     Return low-level information on Docker objects #查看容器详细信息
kill        Kill one or more running containers # kill指定docker容器
load        Load an image from a tar archive or STDIN #从一个tar包或标准输入中加载一个镜像[对应save]
login       Log in to a Docker registry #注册或登录一个docker源服务器
logout      Log out from a Docker registry  # 从当前Docker registry退出
logs        Fetch the logs of a container # 输出当前容器日志信息
pause       Pause all processes within one or more containers # 暂停容器
port        List port mappings or a specific mapping for the container  # 查看映射端口对应的容器内部源端口
ps          List containers # 列出容器列表
pull        Pull an image or a repository from a registry # 从Docker镜像源服务器拉取指定镜像或者库镜像
push        Push an image or a repository to a registry #推送指定镜像会库镜像至docker源服务器
rename      Rename a container  # 
restart     Restart one or more containers  # 重启运行的容器
rm          Remove one or more containers # 移除一个或多个容器
rmi         Remove one or more images # 移除一个或多个镜像（无容器使用的该镜像才可以删除，否则需删除相关容器才可以继续，或者 -f 强制删除）
run         Run a command in a new container  # 创建一个新的容器并运行一个命令
save        Save one or more images to a tar archive (streamed to STDOUT by default)  # 保存一个镜像为一个tar包[对应load]
search      Search the Docker Hub for images  # 在docker hub中搜索镜像
start       Start one or more stopped containers  # 启动容器
stats       Display a live stream of container(s) resource usage statistics #
stop        Stop one or more running containers # 停止容器
tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE #给源中镜像打标签
top         Display the running processes of a container  # 查看容器中运行的进程信息
unpause     Unpause all processes within one or more containers # 取消暂停容器
update      Update configuration of one or more containers  # 
version     Show the Docker version information # 查看docker版本号
wait        Block until one or more containers stop, then print their exit codes  # 截取容器停止时的退出状态


```