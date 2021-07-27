# nginx直接访问静态html

![1](https://gitee.com/ihunzi/images/raw/master/blog/20210714183113.png)

如图，cb这个名字自定义

最主要的就是 **alias**

- root

location块匹配的url为"/demo"，root指令的路径为"/opt/test"，那么，根据上述配置，当我们访问"/demo"这个urI时，实际上访问的是/opt/test/demo路径

配置上述location块后，当我们访问/demo/test1/huge.jpg这个url时，我们访问的是/opt/test/demo/test1/huge.jpg

简单来说，root就是把url路径补在指定路径后面

- alias

location块匹配的url为"/demo"，alias指令的路径为/opt/test 。如你所见，alias指令对应的值也是一个路径，当alias指令与location块结合时，当我们访问/demo/huge.jpg时，其实就是在访问服务器的/opt/test/huge.jpg，也就是说，当我们使用alias时，location的urI是与alias的路径完全对等的

![2](https://gitee.com/ihunzi/images/raw/master/blog/20210714183117.png)

*注意这里color_route.html的位置*

