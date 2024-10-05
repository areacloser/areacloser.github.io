---
author: lanlan2_
title: Ubuntu Server 22.04 避坑和配置指南
time: 2024-10-05 20:54:30 +0800
categories: [Notes, Others]
tags: [virtual-machine, ubuntu]
description: 配置 Ubuntu 虚拟机我踩过的坑和一些提示，可用于借鉴
---

## 前言

先展示一下成果 ~

![neofetch结果](https://i.ibb.co/qBtJQzT/nf.png)
_Neofetch一下_

![lxmusic运作图](https://i.ibb.co/XZp5BSz/lx.png)
_LX Music_

仔细算算，我花在这个虚拟机上的时间已经超过了15个小时，真可谓道阻且长！

话不多说，下面就是正文了

## 开始之前

### 系统信息

我使用的宿主机是Windows7系统，硬件比较古早，这里只展示部分信息：

| 项目       | 值                   |
| :-------- | -------------------: |
| CPU       | Intel i5-2400        |
| 内存       | 8 GB                 |
| 显示适配器  | Intel(R) HD Graphics |

### 软件安装

#### 安装之前的准备

在最最开始前，必不可少的是打开CPU功能中的虚拟化功能（Intel VT-x 或 Hyper-V），不然虚拟机的功能无法正常实现。不过好像这个功能大部分计算机是默认开启的（比如我就读高中的班级电脑），可是我家电脑却不默认开启。

想要开启它，须要进入BIOS设置界面，常规做法是在开机出现Logo时按下F2键，但我家电脑要按Del键，这由主板型号而定。具体可参考[这篇文章](https://zhuanlan.zhihu.com/p/34223088)。

#### SSH & VNC 连接软件

虚拟机自带的用户界面实在难以入眼，加之后续需要建立VNC连接，找一个合适的远程连接软件是必要的。

大部分人SSH连接都会选择使用 XShell， 但它有两个缺点：

1. 不支持VNC连接
2. 终端配色有时会出问题（如运行Neovim时）

因而此处我推荐使用 MobaXterm 作为替代，它虽然没有汉化，但能完美解决 XShell 的问题。

#### 虚拟机软件

市面上常用的虚拟机软件有两个：`VirtualBox` 和 `VMWare`

> 注意！此处有坑！
{: .prompt-tip }

如果各位和我一样是Windows7的钉子户的话，那么请务必 不 要 使用`VirtualBox`！虽然它免费开源，但它出的问题不是我们能够解决的（不支持Windows7）。至于收费的`VMWare`，我想大家应当能各显神通，找到合适的学习版。不过使用Windows7的同学们又要注意了，`VMWare`在 Workstation 15 之后的版本后都不再支持Windows7，所以请不要找更高的版本了！

#### 镜像文件

官网的下载速度十分令人堪忧，建议使用[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/jammy/ubuntu-22.04.5-live-server-amd64.iso)来获取系统镜像。

## 安装系统

具体教程可参考一下几篇教程，都写得很详细：

- [VMware 虚拟机图文安装和配置 Ubuntu Server 22.04 LTS 教程 - 博客园](https://www.cnblogs.com/calvinit/p/16350378.html)
- [vmware中安装 ubuntu 22.04 live-server - 博客园](https://www.cnblogs.com/abc1069/p/16296050.html)
- [vmware 安装 ubuntu server 22.04.2 LTS记录 - 知乎](https://zhuanlan.zhihu.com/p/627023160)


这里基本按着文章里的做就可以了，但有两点我要提醒：

> 注意！此处有坑！
{: .prompt-tip }

1. 配置网络连接时建议先用DHCP（后面可以改回固定IP），不然可能会导致IP冲突。
2. 配置磁盘空间时一定要至少分配40G的空间！不然后续安装图形化界面时会空间不足！这是我亲身经历的，它让我被迫一切从头开始，损失惨重！并且建议将所有的剩余空间（free-space）都用掉，反正留着也没用，不如都分配给根目录（/）

## 配置SSH连接

在进行SSH连接前，要先配置端口转发。同样，推荐一些文章教程：

- [VMware做NAT端口转发(全网最详细步骤) - CSDN](https://blog.csdn.net/weixin_49047967/article/details/132555042)
- [VMware虚拟机配置端口转发（端口映射） - 博客园](https://www.cnblogs.com/testgo/p/10368999.html)

然后是SSH配置：

- [Ubuntu 22.04.3 Server 安装sshd和开启root账号ssh远程 - CSDN](https://blog.csdn.net/gmaaa123/article/details/135162819)
- [如何在 Ubuntu 上启用 SSH（适用于 20.04、22.04）？ - 阿里云](https://developer.aliyun.com/article/1488008)

同样，我有一点要提醒，在SSH连接时，有可能出现`Error503`问题，即建立SSH连接后无法进入命令行界面。虽然其中的原因尚不明确，但是更换端口转发时的IP地址然后重新连接就能解决问题。