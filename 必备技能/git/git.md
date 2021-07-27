# 仓库创建和操作

## 用户操作（安装git时操作一次就行）
### 1、设置用户名
```
git config --global user.name 'nameeeeee'
```
### 2、设置邮箱
```
git config --global user.email '123456@qq.com'
```
### 3、查看信息
```
git config --list
```

## 仓库操作
### 1、创建一个文件夹
>假设新建的目录路径为：E:\jb\cangku
在jb目录下，新建了一个cangku的文件夹

### 2、在文件夹内初始化git（创建git仓库）
```
cd cangku   // 进入目录
git init    // 初始化仓库
```
### git status 查看文件状态

### git add xxx.xx 提交到暂存区

### git commit -m "提交描述"

### 从git删除文件
```
rm -rf xxx.xx   // 先在本地删除文件
git rm xxx.xx   // 从git删除
git commit -m "删除描述"
```

### 提交到远程仓库
```
git push
推送到指定的分支（也可以省略-u）
git push -u origin master
git push -u origin develop
```
>如果第一次提交出错就使用强制覆盖命令
git push -u origin master -f
git push -u origin develop -f
理由是：新创建的远程仓库，和我们本地项目的仓库的内容不一样，向用这个命令，把我们的项目覆盖掉远程仓库的内容，使得远程仓库和我们本地仓库保持一致，在此以后都不会有问题


## 远程仓库管理

### 1、将远程仓库克隆到本地
```
git clone 地址
```


## 分支管理
### 了解
>1.2.8
1——表示大版本更新，架构更改等等
2——小版本更新，新增功能模块等等
8——修复bug

### 打标签
```
git tag -a v1.0.1 -m '版本v1.0.1'
查看所有标签
git tag -l
将新增的标签提交到远程仓库
git push --follow-tags

删除本地标签
git tag -d vxxx
远程标签直接在gitee上删除就行

```

### 常用命令
```
git branch -a   // 查看所有分支
git checkout 分支名 // 切换对应的分支
在某个版本下建分支
git checkout 分支名 v1.0.1
```

