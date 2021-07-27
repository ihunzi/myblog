# 首屏加载优化

## gzip

- 首先安装插件compression-webpack-plugin，安装5.0.1版本，高版本会出错

```
npm install --save-dev compression-webpack-plugin@5.0.1
```

- 然后在vue.config.js里面加入代码：

```vue
首先在开头加入
const webpack = require('webpack')
const CompressionWebpackPlugin = require('compression-webpack-plugin')
const productionGzipExtensions = ['js', 'css']
```

![1](https://gitee.com/ihunzi/images/raw/master/blog/20210714191547.png)

- 在module.exports里面，configureWebpack里面配置下面插件

```vue
Gizp压缩
new CompressionWebpackPlugin({
					algorithm: 'gzip',
					test: new RegExp('\\.(' + productionGzipExtensions.join('|') + ')$'),
					threshold: 10240,
					minRatio: 0.8,
					// deleteOriginalAssets: true,
				})

代码量小的小模块合并
ew webpack.optimize.LimitChunkCountPlugin({
					maxChunks: 5, 
					minChunkSize: 100
				})

代码怎么写？这里使用configureWebpack的函数式编程
configureWebpack: config => {
			
		if (process.env.NODE_ENV === 'production') {
			// 为生产环境修改配置
			
			config.plugins.push(
				new CompressionWebpackPlugin({
					algorithm: 'gzip',
					test: new RegExp('\\.(' + productionGzipExtensions.join('|') + ')$'),
					threshold: 10240,
					minRatio: 0.8,
					// deleteOriginalAssets: true,
				})
			)
			
			config.plugins.push(
				new webpack.optimize.LimitChunkCountPlugin({
					maxChunks: 5, 
					minChunkSize: 100
				})
			
		} else {
			// 为开发环境修改配置
			
		}
	}

```

![2](https://gitee.com/ihunzi/images/raw/master/blog/20210714191550.png)

*因为我们使用的是插件，所以用**config.plugins.push()**来使用插件，每一个插件的使用都要调用一次**config.plugins.push()***

- 配置完了vue.config.js，接着配置nginx.conf

```
加入代码
	gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;
	gzip_disable "MSIE [1-6]\.";

```

<img src="https://gitee.com/ihunzi/images/raw/master/blog/20210714191554.png" alt="3" style="zoom: 67%;" />

然后重启nginx

## 其他优化

- 去除打包产生的map文件

```
productionSourceMap: false, // 是否在构建生产包时生成sourcdeMap
```

- IgnorePlugin用于忽略某些特定的模块

```
让 webpack 不把这些指定的模块打包进去
本项目使用了时间插件moment，如引入了一个插件，只用到了中文语言包，打包的时候把非中文语言包排除掉。
new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)

```

![4](https://gitee.com/ihunzi/images/raw/master/blog/20210714191600.png)

- 去除程序里面的console.log

```
加载插件terser-webpack-plugin，这个插件不用安装，vuecli自带
const TerserPlugin = require('terser-webpack-plugin');
```

<img src="https://gitee.com/ihunzi/images/raw/master/blog/20210714191606.png" alt="5" style="zoom:75%;" />

![6](https://gitee.com/ihunzi/images/raw/master/blog/20210714191609.png)

## CDN加速

```
// 是否生产环境
const isProd = process.env.NODE_ENV === 'production'		

  // 忽略打包的第三方库
let externals = {
	vue: 'Vue',
	// vuex: 'Vuex',
	'vue-router': 'VueRouter',
	axios: 'axios',
	"element-ui": "ELEMENT"
}

// cdn配置
let cdn = {
  js: [
    'https://cdn.jsdelivr.net/npm/vue@2.6.12',
    'https://unpkg.com/vue-router@3.5.1/dist/vue-router.js',
    'https://unpkg.com/axios@0.21.1/dist/axios.min.js',
    'https://unpkg.com/element-ui@2.15.1/lib/index.js'
  ],
  css: [
		'https://unpkg.com/element-ui@2.15.1/lib/theme-chalk/index.css'
	]
}

// 判断环境，如果不是生产环境，就把cdn和externals清空，否则使用上面的值
cdn = isProd ? cdn : {css: [], js: []}
externals = isProd ? externals : {}
```

![7](https://gitee.com/ihunzi/images/raw/master/blog/20210714191616.png)

```
在module.exports里面分两步配置
①首先在configureWebpack里面，配置忽略打包的配置
config.externals = externals

②在chainWebpack配置cdn引入
	chainWebpack: config => {
		// 配置cdn引入
		config.plugin('html').tap(args => {
			args[0].cdn = cdn
			return args
		})
		// 移除prefetch插件，避免加载多余的资源
		config.plugins.delete('prefetch')
	},
其中移除prefetch插件这行代码，就这么加上去吧，好像也没啥影响
```

![8](https://gitee.com/ihunzi/images/raw/master/blog/20210714191619.png)

**然后在public/index.html里面引入cdn**

```
Css
<% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.css) { %>
			<link rel="stylesheet" href="<%= htmlWebpackPlugin.options.cdn.css[i] %>" />
		<% } %>
Js
<% for (var i in htmlWebpackPlugin.options.cdn && htmlWebpackPlugin.options.cdn.js) { %>
			<script src="<%= htmlWebpackPlugin.options.cdn.js[i] %>" crossorigin="anonymous" type="text/javascript"></script>
		<% } %>

```

![9](https://gitee.com/ihunzi/images/raw/master/blog/20210714191623.png)

![10](https://gitee.com/ihunzi/images/raw/master/blog/20210714191625.png)

然后就大功告成了。

（看到一些教程里面把import去掉是没必要的，因为开发环境需要用到这些，去掉之后开发环境就运行不起来了。我们最主要的就是在vue.config.js里面区分了开发环境和生产环境，这样就可以正常运行了。）

参考资料

https://blog.csdn.net/qq_34295211/article/details/104837906

https://www.cnblogs.com/zxuedong/p/12809234.html



## 完整代码

- 完整的vue.config.js代码

```
const path = require('path');//引入path模块
const resolve = dir => {
  return path.join(__dirname, dir)  //path.join(__dirname)设置绝对路径
}

// gzip压缩插件
const webpack = require('webpack')
const CompressionWebpackPlugin = require('compression-webpack-plugin')
const productionGzipExtensions = ['js', 'css']

// 去除console.log的插件
const TerserPlugin = require('terser-webpack-plugin');

// 是否生产环境
const isProd = process.env.NODE_ENV === 'production'		

  // 忽略打包的第三方库
let externals = {
	vue: 'Vue',
	vuex: 'Vuex',
	'vue-router': 'VueRouter',
	axios: 'axios',
	"element-ui": "ELEMENT",
  moment: "moment",
  // "lunar-javascript": "{Solar, Lunar}"
  // 'echarts': 'echarts'
}

// cdn配置
let cdn = {
  js: [
    'https://cdn.jsdelivr.net/npm/vue@2.6.12',
    'https://unpkg.com/vuex@3.6.2',
    'https://unpkg.com/vue-router@3.5.1/dist/vue-router.js',
    'https://unpkg.com/axios@0.21.1/dist/axios.min.js',
    'https://unpkg.com/element-ui@2.15.1/lib/index.js',
    'https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/moment.min.js',
    'https://cdn.bootcdn.net/ajax/libs/moment.js/2.29.1/locale/zh-cn.min.js',
    // 'https://unpkg.com/lunar-javascript@1.2.3/lunar.js'
    // 'https://cdn.bootcdn.net/ajax/libs/echarts/5.1.0/echarts.common.min.js'
  ],
  css: [
		'https://unpkg.com/element-ui@2.15.1/lib/theme-chalk/index.css'
	]
}

// 判断环境，如果不是生产环境，就把cdn和externals清空，否则使用上面的值
cdn = isProd ? cdn : {css: [], js: []}
externals = isProd ? externals : {}

module.exports = {
	publicPath: "./",
	productionSourceMap: false, // 是否在构建生产包时生成sourcdeMap

	chainWebpack: config => {
		// 配置cdn引入
		config.plugin('html').tap(args => {
			args[0].cdn = cdn
			return args
		})

		// 移除prefetch插件，避免加载多余的资源
		config.plugins.delete('prefetch')

    // 给目录配置别名
    config.resolve.alias
        //set第一个参数：设置的别名，第二个参数：设置的路径
        .set('@',resolve('src'))
        .set('components',resolve('src/components'))
        .set('assets',resolve('src/assets'))
        .set('views',resolve('src/views'))
        .set('network',resolve('src/network'))
        .set('store',resolve('src/store'))
	},
	
	configureWebpack: config => {
		// 忽略打包配置
		config.externals = externals
			
		if (process.env.NODE_ENV === 'production') {	// 为生产环境修改配置
      
			/* 忽略moment的语言包 */
			config.plugins.push(new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)),
			
			/* 这里是 compression-webpack-plugin gizp压缩 插件的代码 */
			config.plugins.push(
				new CompressionWebpackPlugin({
					algorithm: 'gzip',
					test: new RegExp('\\.(' + productionGzipExtensions.join('|') + ')$'),
					threshold: 10240,
					minRatio: 0.8,
					// deleteOriginalAssets: true,
				})
			)
			
			/* 小模块代码合并 */
			config.plugins.push(
				new webpack.optimize.LimitChunkCountPlugin({
					maxChunks: 5, 
					minChunkSize: 100
				})
			)
			
			/* 这里是去掉console.log的代码 */
			config.plugins.push(
				new TerserPlugin({
					terserOptions: {
						compress: {
							drop_console: true,
						}
					}
				})
			)
		} else {
			// 为开发环境修改配置
		}
	}
	
}

```

