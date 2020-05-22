# TensorFlow-gpu/Pytorch-gup配置

##  资源：

- 系统：ubuntu16.04.6
- 显卡：TITAN RTX
- Python环境：Anaconda
- tf版本：tensorflow==2.0.0；torch版本：pytorch==1.5

## 1.1安装系统
##1.2 Nvidia驱动安装

- 更改源

> $ sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup     *#备份系统文件*
>
> $ sudo gedit /etc/apt/sources.list      *#修改配置*

- 打开后将里面内容全部删除，替换为[清华开源镜像中16.04](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)的source.list内容（如下）

```txt
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
```

- 接下来添加ppa源
>$ sudo add-apt-repository ppa:xorg-edgers/ppa #添加ppa源
>
>$ sudo add-apt-repository ppa:graphics-drivers/ppa *#添加ppa源*
>
>$ sudo apt-get update *#更新软件包*

- 查看合适版本的nvidia驱动

> $ ubuntu-drivers devices

- 按照型号安装驱动（一般按照推荐进行安装）

> $ sudo apt-get install nvidia-430

- 安装成功后，重启

> $ reboot

- 重启后查看是否安装成功

> $ nvidia-smi

## 1.2 安装Anaconda

- 进入官网下载:https://www.anaconda.com/products/individual
- 打开下载文件的位置

> $ cd Downloads/

- 运行 .sh 文件

> $ bash Anaconda3-5.2.0-Linux-x86_64.sh

- 连续点击enter键，出现yes|no时根据自己需求选择，选择yes为默认安装路径/环境变量信息

- 安装完成后<u>**重启终端**</u>，输入Python即为Anaconda里的Python版本

## 1.3 安装tensorflow-gpu

- 创建虚拟环境

> $ conda creat -n {tf-gpu} python=3.6

- 添加源

> $ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
>
> $ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
>
> $ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/  (安装pytorch时候用）
>
> $ conda config --set show_channel_urls yes

- 安装tensorflow-gpu==2.0.0

> $ conda install tensorflow-gpu==2.0.0

- 安装pytorch-gpu==1.5.0

> $ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/ 
>
> $ conda config --set show_channel_urls yes

> $ conda install pytorch torchvision cudatoolkit=10.0

## 1.4 测试

```python
# 测试torch-gpu
import torch
print(torch.cuda.is_available())
# 测试tensorflow-gpu
import tensorflow
print(tensorflow.is_gpu_available())
"""若返回为True,则证明安装成功"""
```

## 样例-Pytorch

> [**MNIST测试**](https://github.com/dragen1860/Deep-Learning-with-PyTorch-Tutorials/blob/master/lesson29-MNIST测试/main.py)

## 样例-tensorflow

> 





y

y