# DOM

>要操作DOM的Script要写在下面

## 获取元素

- 根据`ID`获取，返回元素对象

```js
var el = document.getElementById('id')
console.log(el)
console.log(typeof el)
console.dir(el)
```

*console.dir 打印返回的元素对象，更好的查看里面的属性和方法*

- 根据`标签名`获取，返回带有指定标签名的对象的**集合**，以伪数组的形式存储

```js
var lis = document.getElementsByTagName('li')
```

- 根据`标签名`获取父元素的子元素

```js
<ul id="id">
  <li>1</li>
  <li>2</li>
  <li>3</li>
</ul>

var lis = document.getElementById('id').getElementsByTagName('li')
var lis = document.getElementsByTagName('ul')[0].getElementsByTagName('li')
```

- 根据`类名`获取，返回集合

```js
var el_Class = document.getElementsByClassName('classname')
```

- 通用选择器

根据指定选择器返回第一个元素对象

```js
var el_className = document.querySelector('.classname')
var el_id = document.querySelector('#id')
var el_tag = document.querySelector('div')
```

根据指定选择返回所有，集合

```js
var el_className = document.querySelectorAll('.classname')
var el_id = document.querySelectorAll('#id')
var el_tag = document.querySelectorAll('div')
```

- 获取`body`

```js
var body = document.body;
```

- 获取`html`

```js
var html = document.documentElement;
```

## 操作元素

### 改变元素内容

```js
var el = document.getElementById('id')

// 写
el.innerText = 'text'
el.innerHtml = '<strong>text</strong>'

// 读
console.log(el.innerText)		// 会去除空格和换行
console.log(el.innerHtml)		// 保留
```

### 改变元素属性

`src`、`href`、`id`、`alt`、`title`

```js
var el = document.getElementById('id')

// 获取默认的属性值
el.属性 = 值
el.src = 'xx/xx/xx'

// 获取自定义的属性
el.getAttribute('属性', '值')
```

#### 自定义属性

H5规定自定义属性`data-`开头做为属性名并且赋值

```js
<div data-index="1"></div>

// 设置自定义属性
el.setAttribute('属性', '值')

// 移除自定义属性
el.removeAttribute('属性', '值')
```

H5新增的自定义属性的方法（IE 11+才支持），他只能获取`data-`开头的

```js
el.dataset.index
el.dataset['index']		// 有 - 要注意驼峰
```

### 改变表单属性

`type`、`value`、`checked`、`selected`、`disableed`

```js
var input = document.querySelector('input')

input.value = '123'
input.disableed = true
```

### 改变样式属性

产生行内样式，css权重较高

```js
var el = document.getElementById('id')

el.style.backgroundColor = 'pink'

/* css */
.change {
    /* css */
}

el.className = 'change'
el.className = 'new change'
```

## 节点操作

### 父节点

```js
// 获取子节点 el
var el = document.getElementById('id')

// 离el元素最近的父级节点，找不到就返回null
el.parentNode
```

### 子节点

```js
// 获取父节点 el
var el = document.getElementById('id')

// 返回所有的子节点，包含元素节点，文本节点等等。。。（一般不推荐使用childNodes）
el.childNodes

// 返回所有的元素节点
el.children

// 获取第一个子节点
el.firstChild
el.firstElementChild
el.children[0]（推荐）

// 获取最后一个子节点
el.lastChild
el.lastElementChild
el.children[el.children.length-1]（推荐）
```

### 兄弟节点

```js
el.nextSibling
el.nextElementSibling
el.previousSibling
el.previousElementSibling
```

### 创建节点

```js
var div1 = document.createElement('div')
```

### 添加节点

```js
node.appendChild(child)		// node是父， child是子

el.appendChild(div1)		// 这个div1是上面的div1
```

在前面插入

```js
node.insertBefore(child, 指定元素)	
```

### 删除节点

```js
node.removeChild(child)	
```

### 复制节点

```js
var newNode = node.cloneNode()		// 括号为空或者里面是 false浅拷贝只复制标签不复制里面的内容
var newNode = node.cloneNode(true)		// 括号为true深拷贝复制标签复制里面的内容
然后用添加节点，添加
```

## 事件

事件源

```js
var btn = document.getElementById('btn')
```

事件类型，比如这里的`onclick`

事件处理程序

```js
btn.onclick = function() {
    alert('处理')
}
```

唯一性，只能有一个onclick，后面的会覆盖前面的

### 事件侦听注册事件

```js
btn.addEventListener('click', function() {
    alert(22);
})

function fn() {
    alert(33);
}

btn.addEventListener('click', fn)
```

解决唯一性的问题

**冒泡阶段如果 addeventlistener第三个参数是 false或者省略那么则处于冒泡阶段**

### 删除事件

传统方式

```js
btn.onclick = null
```

方法侦听注册方式

```js
btn.removeEventListener('click', fn)
```

### 事件对象

>1. event就是一个事件对象写到我们侦听函数的小括号里面当形参来看
>2. 事件对象只有了事件才会存在，它是系统给我们自动创建的，不需要我们传递参数
>3. 事件对象是我们事件的一系列相关数据的集合跟事件相关的比如鼠标点击里面就包含了鼠标的相关信息，鼠标坐标啊，如果是键盘事件里面就包含的键盘事件的信息比如判断用户按下了那个键
>4. 这个事件对象我们可以自己命名比如 event、evt、e

```js
btn.onclick = function(event) {
    console.log(event)
}
```

- `e.target`返回的是触发事件的对象（元素），this返回的是绑定事件的对象（元素）

- `e.type`返回事件类型

#### 阻止默认行为

```js
// 如 a 标签的跳转功能
var a = document.querySelector('a')
a.addEventListener('click', function(e) {
    e.preventDefault();		// 阻止默认行为 dom 写法
})

a.onclick = function(e) {
    e.preventDefault();
}
```

#### 阻止冒泡

```js
e.stopPropagation();
```

#### 事件委托

不是每个子节点单独设置事件监听器，而是事件监听器设置在其父节点上，然后利用冒泡原理影响设置每个子节点