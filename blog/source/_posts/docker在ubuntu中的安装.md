---
title: docker在ubuntu中的安装
date: 2017-10-15 17:46:00
categories:
  - dokcer
  - linux
tags:
  - linux
---

工作中需要使用docker发布web app,于是学习了docker的基本使用以及docker ce 环境的安装。本文主要以docker ce 的安装(ubuntu 17.04, docker 17.06.0-ce)。

<!-- more -->

## docker ce 的安装

### 卸载系统可能存在的旧版本docker
``` bash
$ sudo apt remove docker docker-engine docker.io
```

### 安装docker环境需要的依赖
``` bash
$ sudo apt update
$ sudo apt install linux-image-extra-$(uname -r) linux-image-extra-virtual
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

### 添加docker 官方GPG密钥
``` bash
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### 添加 docker 软件源
``` bash
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

** 提醒: ** 如果无法连接docker官方源可以使用国内源，比如阿里源
``` bash
$ curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```


### 安装docker-ce
``` bash
$ sudo apt-get install docker-ce
```

** 提醒: ** 如果想安装特版本使用一下命令,其中VERSION是版本号
``` bash
$ sudo apt-get install docker-ce=<VERSION>
```

### docker ce 卸载
需要通过卸载 docker-ce 以及删除对应docker的文件夹数据
``` bash
$ sudo apt purge docker-ce && sudo rm -rf /var/lib/docker
```

## 其它
如果需要使用到私有的镜像源并且没有走https的连接，可以修改文件 /etc/systemd/system/multi-user.target.wants/docker.service,
修改ExecStart这个参数即可，例如:
假设私有镜像地址为: http://registry.private.com
则将ExecStart内容改为:
```
ExecStart=/usr/bin/dockerd -H fd:// --insecure-registry=http://registry.private.com
```
