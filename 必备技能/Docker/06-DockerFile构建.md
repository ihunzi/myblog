# Dockerfile

## 介绍
dockerfile是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1、 编写一个dockerfile文件

2、 docker build 构建称为一个镜像

3、 docker run运行镜像

4、 docker push发布镜像（DockerHub 、阿里云仓库)

## 基础知识
1、每个保留关键字(指令）都是必须是大写字母

2、执行从上到下顺序

3、#表示注释

4、每一个指令都会创建提交一个新的镜像层，并提交！

Dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！

Docker镜像逐渐成企业交付的标准，必须要掌握！

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行产品。

Docker容器：容器就是镜像运行起来提供服务。

## 指令
**所有指令都是大写**
```
FROM          # from:基础镜像，一切从这里开始构建
MAINTAINER    # maintainer:镜像是谁写的， 姓名+邮箱
RUN           # run:镜像构建的时候需要运行的命令
ADD           # add:步骤，tomcat镜像，这个tomcat压缩包！添加内容 添加同目录
WORKDIR       # workdir:镜像的工作目录
VOLUME        # volume:挂载的目录
EXPOSE        # expose:保留端口配置
CMD           # cmd:指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT    # entrypoint:指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD       # onbuild:当构建一个被继承DockerFile这个时候就会运行onbuild的指令，触发指令
COPY          # copy:类似ADD，将我们文件拷贝到镜像中
ENV           # env:构建的时候设置环境变量！

```

## 创建一个自己的centos
```
# 1./home下新建dockerfile目录
$ mkdir dockerfile

# 2. dockerfile目录下新建mydockerfile-centos文件
$ vim mydockerfile-centos

# 3.编写Dockerfile配置文件
FROM centos							# 基础镜像是官方原生的centos
MAINTAINER hunzi<lidf999@163.com> 	# 作者

ENV MYPATH /usr/local				# 配置环境变量的目录 
WORKDIR $MYPATH						# 将工作目录设置为 MYPATH

RUN yum -y install vim				# 给官方原生的centos 增加 vim指令
RUN yum -y install net-tools		# 给官方原生的centos 增加 ifconfig命令

EXPOSE 80							# 暴露端口号为80

CMD echo $MYPATH					# 输出下 MYPATH 路径
CMD echo "-----end----"				
CMD /bin/bash						# 启动后进入 /bin/bash

# 4.通过这个文件构建镜像
# 命令： docker build -f 文件路径 -t 镜像名:[tag] .
$ docker build -f mydockerfile-centos -t mycentos:0.1 .

# 5.出现Successfully则构建成功
Successfully built b8cf5d647611
Successfully tagged mycentos:0.1

查看镜像
docker images

# 6.测试运行
$ docker run -it mycentos:0.1 		# 注意带上版本号，否则每次都回去找最新版latest

$ pwd	
/usr/local      # 与Dockerfile文件中 WORKDIR 设置的 MYPATH 一致
$ vim	        # vim 指令可以使用
$ ifconfig      # ifconfig 指令可以使用

# docker history 镜像id 查看镜像构建历史步骤
$ docker history 镜像id

```
### CMD 和 ENTRYPOINT区别
```
CMD               # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代。
ENTRYPOINT        # 指定这个容器启动的时候要运行的命令，可以追加命令
```