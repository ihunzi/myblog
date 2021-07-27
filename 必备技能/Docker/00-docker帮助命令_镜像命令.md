# 常用命令
## 帮助命令
```
docker version    #显示docker的版本信息。
docker info       #显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help #帮助命令
```
## 镜像命令
```
docker images #查看所有本地主机上的镜像 可以使用docker image ls代替
docker search 搜索镜像
docker pull 下载镜像 docker image pull
docker rmi 删除镜像 docker image rm
```
### 显示本地已有的镜像

- docker images

```
[root@VM-8-17-centos /]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
mysql        latest    c0cdc95609f1   12 days ago   556MB

# 解释
#REPOSITORY			# 镜像的仓库源
#TAG				# 镜像的标签
#IMAGE ID			# 镜像的id
#CREATED			# 镜像的创建时间
#SIZE				# 镜像的大小
# 可选项
Options:
  -a, --all             Show all images (default hides intermediate images) #列出所有镜像
      --digests         Show digests #列出摘要
  -f, --filter filter   Filter output based on conditions provided #根据提供的条件过滤输出
      --format string   Pretty-print images using a Go template #使用Go模板输出漂亮的镜像
      --no-trunc        Don't truncate output #不截断输出
  -q, --quiet           Only show image IDs #只列出镜像IDs
  
[root@VM-8-17-centos /]# docker images -aq #显示所有镜像的id
c0cdc95609f1

```
### 搜索镜像

- docker search

```
[root@VM-8-17-centos /]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10916     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4122      [OK]       
...
...

# --filter=STARS=3000 #搜索出来的镜像就是STARS大于3000的
Options:
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output
      
[root@VM-8-17-centos /]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   10916     [OK]       
mariadb   MariaDB Server is a high performing open sou…   4122      [OK]       

```
### 下载镜像

- docker pull [imageName]

```
# 下载镜像 docker pull 镜像名[:tag]
[root@VM-8-17-centos /]# docker pull tomcat:8
8: Pulling from library/tomcat #如果不写tag，默认就是latest
d960726af2be: Pull complete    #分层下载： docker image 的核心 联合文件系统
e8d62473a22d: Pull complete 
8962bc0fad55: Pull complete 
65d943ee54c1: Pull complete 
da20b77f10ac: Pull complete 
fb6a778e6477: Pull complete 
ae7884f0e61b: Pull complete 
5a229cc6592e: Pull complete 
1c2d6d25c04a: Pull complete 
39b99cdc3584: Pull complete 
Digest: sha256:2f98644d7951b04870bde8f1565225f72d84cc895c38a483630730a1e9d363de # 签名
Status: Downloaded newer image for tomcat:8
docker.io/library/tomcat:8 #真实地址

#等价于
docker pull tomcat:8
docker pull docker.io/library/tomcat:8
```
>分层可以理解为：当下次再下载另一个版本时，如果已经下载过的东西就不会再次下载了，已经有的可以共用

### 删除镜像

- docker rmi

```
docker rmi -f 镜像id #删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id 镜像id #删除指定的镜像
docker rmi -f $(docker images -aq) #删除全部的镜像
```