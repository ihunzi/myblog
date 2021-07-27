# externals
>Externals 配置项用来告诉 Webpack 要构建的代码中使用了哪些不用被打包的模块，也就是说这些模版是外部环境提供的，Webpack 在打包时可以忽略它们。
## 用法
```
作用于vue.config.js

// 忽略打包的第三方库
let externals = {
      vue: 'Vue',
      vuex: 'Vuex',
      'vue-router': 'VueRouter',
      axios: 'axios',
      "element-ui": "ELEMENT",
      moment: "moment",
      "lunar-javascript": "{Solar, Lunar}"
      echarts: 'echarts'
}

module.exports = {
  configureWebpack: config => {
    // 忽略打包配置
    config.externals = externals
  }
}
或者
module.exports = {
  configureWebpack: config => {
    // 忽略打包配置
    config.externals = {
      vue: 'Vue',
      vuex: 'Vuex',
      'vue-router': 'VueRouter',
      axios: 'axios',
      "element-ui": "ELEMENT",
      moment: "moment",
      "lunar-javascript": "{Solar, Lunar}"
      echarts: 'echarts'
    }
  }
}

```
### externals的模块属性理解
```
// 忽略打包的第三方库
let externals = {
      vue: 'Vue',
      vuex: 'Vuex',
      'vue-router': 'VueRouter',
      axios: 'axios',
      "element-ui": "ELEMENT",
      moment: "moment",
      "lunar-javascript": "{Solar, Lunar}"
      echarts: 'echarts'
}
```
>为什么这么写？为什么vue对应的是'Vue'、element-ui对应的是"ELEMENT"、lunar-javascript对应的是"{Solar, Lunar}"？为什么键有的加了引号，有的没加引号
- 对象写成键值的形式，格式为'aaa': 'bbb'
- aaa表示要引入的资源的名字, 一般请情况下aaa(键)，可以不用加引号，但是如果键的命名比较特殊比如xxx-xxx-xxx用‘-’连接的，就需要加引号
- **bbb(值)不能乱写**，按照我的理解可以分成两种情况
  - 1、表示该模块提供给外部引用的名字，即JS模块暴露对外的接口
    >查找源码可以找到（可以搜索一些关键字：global、export、Vue.prototype、Window.等等）。
    >比如element-ui
    >1、在node_modules文件夹下面找到element-ui文件夹
    2、找到src（一般源码存放在src文件夹下面）里面的index.js（一般模块的入口文件是index.js）
    3、可以看到里面有一行Vue.prototype.$ELEMENT，这里可以初步判断element-ui对应的是"ELEMENT"
    4、可以尝试一下，如果不是的话（反正2.15.1版本就是）再试试ElementUi，或者自己找找还有没有其他的

    >比如vue-router
    >1、在node_modules文件夹下面找到vue-router文件夹
    2、找到src（一般源码存放在src文件夹下面）里面的index.js（一般模块的入口文件是index.js）
    3、就能看到有一个export default class VueRouter {...}，所以就是VueRouter

  - 2、在项目中引入的名字
    >在调用模块时引入的名字，如const xxx = require('../../')、import xxx from '../../'，其中xxx就是引入的名字
    
    >比如moment
    >在组件中使用时：import moment from 'moment'

    >比如lunar-javascript
    >在组件中使用时：import { Solar, Lunar } from "lunar-javascript"

