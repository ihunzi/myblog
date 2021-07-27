# Miniconda

## 基本操作

```
conda create -n 你想要的名字
示例：conda create -n tf python=3.8 tensorflow=2.3

查看所有环境
conda info -e

切换环境
conda activate tf

退出环境
conda deactivate

删除环境
conda remove -n 环境名 --all

在环境中安装模块（python包）
（推荐）
conda install 模块名
conda install 软件名=版本号
也可以用
pip3 install 模块名
pip3 install 模块名==版本号
```

## 下载

```
命令直接下载
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh

或者清华大学镜像站miniconda下载地址:
https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/
```

![Snipaste_2021-07-09_14-54-12](https://gitee.com/ihunzi/images/raw/master/blog/20210714185932.png)

然后用Xftp传到服务器上去

## 安装

```
bash Miniconda3-latest-Linux-x86_64.sh
```

默认安装的话，他会提示你安装路径在哪

![Snipaste_2021-07-09_15-12-52](https://gitee.com/ihunzi/images/raw/master/blog/20210714185936.png)

## 激活环境

- 1、进入/root/miniconda3/bin/目录下，执行./conda init或者./conda init bash

```py
cd /root/miniconda3/bin
./conda init或者./conda init bash
如果出现(base)
(base) [root@hecs-x-medium-2-linux-20200620162454 bin]#
则成功，否则看2
```

- 2、检查环境变量，再执行source

```
查看是否加到环境变量
vim /root/.bashrc
```

![Snipaste_2021-07-09_15-17-18](https://gitee.com/ihunzi/images/raw/master/blog/20210714185940.png)

```
退出重启环境变量
source /root/.bashrc
会自动进入(base)环境
(base) [root@hecs-x-medium-2-linux-20200620162454 bin]#
```

- 退出base环境

```
conda deactivate
```

- 查看版本

```
conda -V
```



## 修改镜像源

```
首先执行
vim /root/.condarc
然后去这个网站里面
https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/
复制
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  
 然后wq保存退出
 再执行
 conda clean -i
```

至此，安装完成

