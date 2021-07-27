# 将vue项目上传到gitee管理

## 一、准备工作

>首先在gitee上面创建一个新仓库
用REAMD初始化仓库，设置分支可以是master或者master/develop或者其他

### 获取仓库的ssh访问链接
>git@gitee.com:iHunzi/front-end-learning.git

### 创建一个Vue的项目（vue cli），已有项目忽略。。。
### 进入项目工程目录
> cd xx/xx/xx

## 二、将本地仓库与gitee的远程仓库关联
### 在vue项目目录下，将这个目录设置成git本地仓库
>git init
### 将本地仓库与远程仓库关联
```
git remote add origin 远程仓库地址
(origin默认的远程仓库名字，可以重命名)
git remote add origin git@gitee.com:iHunzi/front-end-learning.git
```
## 三、提交代码
### 首次提交代码到master主分支
```
1、git status
2、git add *
3、git commit -m "提交描述"
~~git push~~
~~git push -u origin master~~
（一般情况下这里会报错，所以不要用git push或者git push -u origin master）
报错原因：新创建的远程仓库，和我们本地项目的仓库的内容不一样
4、git push -u origin master -f
-f命令把我们的项目强制覆盖掉远程仓库的内容，使得远程仓库和我们本地仓库保持一致，在此以后都不会有问题
提交完成
```
#### 后续提交代码到master主分支
```
1、git status
2、git add *
3、git commit -m "提交描述"
4、git push
或者git push -u origin master
都行
```


### 首次提交到develop分支
>develop分支是开发分支，正在开发的版本可以放到这个分支
如果gitee没有develop这个分支就新建一个

#### 查看本地分支
```
git branch -a   // 查看所有分支
查看是否有develop分支，没有的话新建一个
git branch develop
然后切换到develop分支
git checkout develop
或者直接使用命令
git checkout -b develop
创建develop并切换到develop分支
```
#### 首次提交代码到develop（先切换到develop分支）
```
1、git status
2、git add *
3、git commit -m "提交描述"
~~git push~~
~~git push -u origin develop~~
（一般情况下这里会报错，所以不要用git push或者git push -u origin develop
报错原因：新创建的远程仓库，和我们本地项目的仓库的内容不一样
4、git push -u origin develop -f
-f命令把我们的项目强制覆盖掉远程仓库的内容，使得远程仓库和我们本地仓库保持一致，在此以后都不会有问题
提交完成
```
#### 后续提交代码到develop（先切换到develop分支）
```
1、git status
2、git add *
3、git commit -m "提交描述"
4、git push
或者git push -u origin develop
都行
```
## 四、版本标签
### 了解
>1.2.8
1——表示大版本更新，架构更改等等
2——小版本更新，新增功能模块等等
8——修复bug

### 打标签（master）
```
git tag -a v1.0.1 -m '版本v1.0.1'
查看所有标签
git tag -l
将新增的标签提交到远程仓库
git push --follow-tags(该命令是追随标签提交)
git push是提交到对应的分支，没有标签可言

```
### 打标签（develop）
```
git tag -a 'v1.0.0-Building-20210530' -m '开发版提交20210530'
建立追踪关系，设置本地仓库的分支（好像默认都有origin）对应远程仓库的分支
git push --set-upstream origin develop（这条命令要在commit之后打完标签之后，只是在第一次的是需要）
将新增的标签提交到远程仓库
git push --follow-tags(该命令是追随标签提交)
git push是提交到对应的分支，没有标签可言

```
```
git tag -a 'v1.1.0-Building-202107032' -m '开发版提交20210703'
```
