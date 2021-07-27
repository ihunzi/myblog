# vue项目部署——Nginx

> 将vue项目在nginx服务器下进行部署

## 环境

`centos` 7.6

`nginx` 1.9.9

## Nginx安装

点击下面参考链接

[centOS7安装nginx及nginx配置](https://blog.csdn.net/qq_37345604/article/details/90034424)

安装完成之后，进入nginx目录，路径如下

```
/usr/local/nginx
```

![image-20210727112808767](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727112808767.png)

主要使用到的有三个目录：

`conf`：nginx配置文件的目录

`hmtl`：项目打包文件存放的目录

`sbin`：Nginx启停的目录

### conf

![image-20210727113537536](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727113537536.png)

conf目录里面主要用到两个文件

`cert`：给域名配置ssl证书时，存放key的目录

`nginx.conf`：nginx服务配置的文件

#### nginx.conf

在配置文件的http模块内加入以下配置

```
	server {
		listen 443 ssl;
		#配置HTTPS的默认访问端口为443。
		#如果未在此处配置HTTPS的默认访问端口，可能会造成Nginx无法启动。
		#如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on。
		server_name www.sunweg.com; #需要将yourdomain.com替换成证书绑定的域名。
		root html;
		index index.html index.htm;
		ssl_certificate cert/5605649_www.sunweg.com.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
		ssl_certificate_key cert/5605649_www.sunweg.com.key; #需要将cert-file-name.key替换成已上传的证书密钥文件的名称。
		ssl_session_timeout 5m;
		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
		#表示使用的加密套件的类型。
		ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
		ssl_prefer_server_ciphers on;
		
		gzip on;
		gzip_min_length 1k;
		gzip_comp_level 9;
		gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
		gzip_vary on;
		gzip_disable "MSIE [1-6]\.";
		
		location /rs {
			root html;  #站点目录。
			index index.html index.htm;
	       	try_files $uri $uri/ /rs/index.html;
		}
	
	}
```

![image-20210727114232906](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727114232906.png)

ssl证书的配置，在阿里云、华为云等等都有说明。

### html

![image-20210727114351820](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727114351820.png)

这里把项目打包文件，放到rs目录中。

**配置完上面这些东西后，重启nginx**

首先进入`sbin`目录下，然后执行重启命令。

```
cd /usr/local/nginx/sbin

./nginx -s reload
```

重启没报错之后，可以用域名访问网页了。

## vue项目部署

### 项目打包

首先把项目打包，执行命令

```
npm run build
```

在项目目录里面会生成一个dist文件夹，里面就是打包过后的文件。

**注意事项**

打包需要注意包的大小，如果太大，会影响网站第一次打开的速度，首屏加载太慢会十分影响用户体验！

- Gzip

开启gzip之后，会大大压缩打包的大小，可以网上搜索 关键字`vue gzip`

或者参考链接：[解决！！前端项目打包后生成的chunk-vendors文件过大，导致加载太慢](https://blog.csdn.net/weixin_43850586/article/details/103915473)

- 去除一些无关代码

比如console.log()

有的项目引用了`moment`，这个包的体积太大了，可以配置忽略moment的语言包不打包到项目中，等等。。。

- CDN优化

cdn优化是优化效果最明显的。我们项目用到的依赖包，比如：vue.js、vuex、vue-router、axios、element、echats等等，都可以配置cdn优化。[vue项目打包及cdn优化](https://blog.csdn.net/qq_33316107/article/details/109592386)

如果原来的打包体积有几百k，配置cdn优化之后打包体积可以小到十几k。

### 部署

1、首先在服务器中的Nginx目录的html目录下，新建一个用来存放我们这个项目的文件夹，比如这里新建一个文件夹叫`rs`，然后把我们的打包文件上传到里面。

![image-20210727141044815](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727141044815.png)

2、准备一个二级域名：`rs.xxx.com`（一级域名也可以），打开conf目录下的nginx.conf文件，在http{}中加入下面配置（这里启用了https协议）

```
	server {
		listen 443 ssl;
		#配置HTTPS的默认访问端口为443。
		#如果未在此处配置HTTPS的默认访问端口，可能会造成Nginx无法启动。
		#如果您使用Nginx 1.15.0及以上版本，请使用listen 443 ssl代替listen 443和ssl on。
		server_name rs.sunweg.com; #需要将yourdomain.com替换成证书绑定的域名。
		root html;
		index index.html index.htm;
		ssl_certificate cert/5835764_rs.sunweg.com.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
		ssl_certificate_key cert/5835764_rs.sunweg.com.key; #需要将cert-file-name.key替换成已上传的证书密钥文件的名称。
		ssl_session_timeout 5m;
		ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
		#表示使用的加密套件的类型。
		ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
		ssl_prefer_server_ciphers on;
		
		gzip on;
		gzip_min_length 1k;
		gzip_comp_level 9;
		gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
		gzip_vary on;
		gzip_disable "MSIE [1-6]\.";

		location / {
			root   html/rs;
			index  index.html index.htm;
			try_files $uri $uri/ /index.html;
		}

		#error_page  404              /404.html;

		# redirect server error pages to the static page /50x.html
		#
		error_page   500 502 503 504  /50x.html;
		location = /50x.html {
			root   /usr/share/nginx/html;
		}
	}
```

![image-20210727141709386](https://gitee.com/ihunzi/images/raw/master/blog/image-20210727141709386.png)

然后重启nginx

最后浏览器输入rs.xxx.com就可以访问网站了。

