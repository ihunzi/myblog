# vue+ngxin部署（项目隔离）

## 准备
>首先要有一个域名
每一个项目用一个二级域名，以达到隔离的目的
这里使用 **abc.sunweg.com**

## nginx.conf不分离的配置
### 在http{}里面的加入以下代码
```
	server {
		listen       80;
		server_name  abc.sunweg.com;

		#access_log  /var/log/nginx/host.access.log  main;
		
		gzip on;
		gzip_min_length 1k;
		gzip_comp_level 9;
		gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
		gzip_vary on;
		gzip_disable "MSIE [1-6]\.";

		location / {
			root   /usr/local/nginx/html/dao;
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
- 代码重点
>1、server_name  abc.sunweg.com;   // server_name 改成二级域名
2、root   /usr/local/nginx/html/dao;    // 最后面加一个项目文件夹的名字，这里是dao，这样的话，在vue里面就可以把dao改成./
## nginx.conf分离的配置
>在nginx的conf.d文件夹里面，有一个default.conf的文件
copy这个文件，重新命名abc.sunweg.com.conf
然后根据上面的代码修改即可
