# django部署

## 环境

- centos7.6或者ubuntu18.4
- python3.6.9（ubuntu18.4自带）
- 虚拟环境
- django2.2.19

## 依赖包

```python
# 基本依赖
pip install uwsgi
pip install Django==2.2.19
pip install pymysql

# 爬虫项目依赖
安装pandas 
pip install pandas
安装selenium
pip install selenium
安装bs4
pip install bs4
```

## 试运行

```python
python3 manage.py runserver 0.0.0.0:7999
能不能运行项目
如果运行项目出错，则根据错误内容解决问题
我这里遇到的有
安装跨域配置
pip install django-cors-headers
然后在setting.py配置跨域相关的代码

pip install requests


修改编码问题
/usr/local/lib/python3.6/dist-packages/django/db/backends/mysql/operations.py
146行
把
query = query.decode(errors='replace')
改成
query = query.encode(errors='replace') if isinstance(query, str) else query.decode(errors='replace')
然后就可以成功运行django项目。
```

## 搭建环境

上面这样运行跟在本地运行没什么区别，所以需要用nginx+uwsgi在搭建服务

首先在项目（hunzi）里面，新建一个uwsgi_conf的文件夹

（我的工程路径为/home/djangoProject/hunzi）

![1](https://gitee.com/ihunzi/images/raw/master/blog/20210714184130.png)

在这个文件夹里面，新建一个叫uswgi.ini的文件

配置如下：

Django使用7999端口![2](https://gitee.com/ihunzi/images/raw/master/blog/20210714184133.png)

然后配置nginx的nginx.conf

配置如下，

在80server下面新建一个server，用来监听8001端口，然后反向代理到7999端口（在这之前要先把华为云服务器的安全组8001端口开放一下）![3](https://gitee.com/ihunzi/images/raw/master/blog/20210714184138.png)

![4](https://gitee.com/ihunzi/images/raw/master/blog/20210714184140.png)

保存配置，重启一下nginx

然后在任意路径下运行

uwsgi --ini /home/djangoProject/hunzi/uwsgi_conf/uwsgi.ini & /usr/local/nginx/sbin/nginx![5](https://gitee.com/ihunzi/images/raw/master/blog/20210714184142.png)

用ps aux | grep uwsgi命令可以查看进程![6](https://gitee.com/ihunzi/images/raw/master/blog/20210714184145.png)

这里有5个进程，因为上面uwsgi.ini里面的process设置里。这样就说明服务器启动成功了。

可以用链接访问了

## uwsgi启停

uWSGI 通过 xxx.ini 启动后会在相同目录下生成一个 xxx.pid 的文件，里面只有一行内容是 uWSGI 的主进程的进程号。

 ```python
 uWSGI 启动：
 uwsgi --ini xxx.ini
 
 uwsgi 重启：
 uwsgi --reload xxx.pid 
 
 uwsgi 停止：
 uwsgi --stop xxx.pid
 ```

