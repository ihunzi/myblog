# vue+docker+ngxin部署

## 一、准备
###安装Docker
>省略
### Docker安装Nginx
```
docker pull nginx
```
### 用Docker随便启动一个容器
```
docker run -d --name nginx01 nginx
```
### 将容器下的文件拷贝到本地
```
在/home/nginx目录下新建一个目录Msater
$ docker cp nginx01:/etc/nginx/nginx.conf /home/nginx/Master/   #把容器中的nginx.conf文件复制到Master目录下
$ docker cp nginx01:/etc/nginx/conf.d/default.conf /home/nginx/Master/    #把容器中的default.conf文件复制到Master目录下
```
### 删除nginx01镜像
```
docker rm -f nginx01
```
### 创建真正的反向代理nginx服务容器（主）
```
docker run -it -d --name MasterNginx -p 80:80 -v /home/nginx/Master/html:/usr/share/nginx/html -v /home/nginx/Master/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/Master/default.conf:/etc/nginx/conf.d/default.conf  -v /home/nginx/Master/logs:/var/log/nginx nginx

命令说明：
docker run -it -d --name MasterNginx  #名称
-p 80:80  # 端口映射
-v /home/nginx/Master/html:/usr/share/nginx/html  # 挂载资源路径
-v /home/nginx/Master/nginx.conf:/etc/nginx/nginx.conf  #挂载主配置文件
-v /home/nginx/Master/default.conf:/etc/nginx/conf.d/default.conf   # 挂载默认配置文件，反向代理就是从这个配置文件中配置（第三个）
-v /home/nginx/Master/logs:/var/log/nginx nginx # 挂载日志文件

2021.06.05说明
最好把第三个指令的改成
-v /home/nginx/Master/conf:/etc/nginx/conf.d
然后项目配置参考vue+docker+ngxin部署.md
```
### 查看容器挂载情况
```
docker inspect MasterNginx
```
- 其中 /home/nginx/Master/html 表示存放的打包的资源根目录，也就是访问 80 端口时 html 作为根目录，放一个简单的 index.html 即可测试。
### 配置文件说明
>这里的nginx服务使用了nginx 分离配置文件 conf.d和default.conf

#### 1、在 nginx.conf 文件中引用 conf.d 下的所有配置文件
```
#在http配置节的末尾添加配置引用
http {
    ...

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```
#### 2、 在 conf.d 中添加 default.conf 配置文件
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    
    #代理配置
    location /business {
        proxy_pass  http://business.app.com;
    }
    
    #代理配置
    location /user {
        proxy_pass  http://user.app.com;
    }
}
```
