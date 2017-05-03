---
title: shadowsocks和polipo配置全局代理
tags:
  - shadowsocks
  - Polipo
  - Linux
  - 代理
categories:
  - 其他
  - 网络
---
<p>
    本文从 http://blog.csdn.net/jon_me/article/details/53525059 迁移过来(<font size="5" color="red">但是不存在侵权问题</font>),做了一些校验和修正
</p>
# Shadowsocks + Polipo 配置全局代理(Linux 版本)
** 以下配置均不在virtualenv 环境里面配置 **
#### 1. **服务器端配置**
- **安装shadowsocks 环境**
    - pip 安装：
    ```bash
    $ sudo pip install shadosocks
    ```
    - 下载python 源码安装：[shadowsocks 链接](https://github.com/shadowsocks/shadowsocks/tree/master)

- ** 配置shadowsocks 启动json文件 **（[参考链接](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)）
    文件路径:
    ```bash
    $ sudo vim /etc/shadowsocks.json
    ```
    shadowsocks.json 文件内容:
    ```josn
    {
        "server":"my_server_ip",
        "server_port":8388,
        "local_address": "127.0.0.1",
        "local_port":1080,
        "password":"mypassword",
        "timeout":300,
        "method":"rc-md5",
        "fast_open": false,
        "workers": 100
    }
    ```

字段名|描述|样例
:---:|:---:|:---:
server|服务器ip或者服务器域名|"127.0.0.1"
server_port|shadowsocks监听端口|8388
local_address|服务器本地端口|1080
password|客户端登陆时密码|"mypassword"
timeout|超时|300
method|传输加密方式|"rc-md5"
fast_open|TCP加速（需要linux内核 3.7.1 以上）|false
workers|最大连接数量|100

- **启动shadowsocks服务**
    启动：
    ``` bash
    $ ssserver -c /etc/shadowsocks.json -d start
    ```
    关闭：
    ```bash
    $ ssserver -c /etc/shadowsocks.json -d stop
    ```

    ssserver 简单说明（其实使用ssserver -h 即可查看帮助,以下内容为复制粘贴得到）：
    ``` bash
    $ ssserver -h
    usage: ssserver [OPTION]...
    A fast tunnel proxy that helps you bypass firewalls.

    You can supply configurations via either config file or command line arguments.

    Proxy options:
      -c CONFIG              path to config file
      -s SERVER_ADDR         server address, default: 0.0.0.0
      -p SERVER_PORT         server port, default: 8388
      -k PASSWORD            password
      -m METHOD              encryption method, default: aes-256-cfb
      -t TIMEOUT             timeout in seconds, default: 300
      --fast-open            use TCP_FASTOPEN, requires Linux 3.7+
      --workers WORKERS      number of workers, available on Unix/Linux
      --forbidden-ip IPLIST  comma seperated IP list forbidden to connect
      --manager-address ADDR optional server manager UDP address, see wiki

    General options:
      -h, --help             show this help message and exit
      -d start/stop/restart  daemon mode
      --pid-file PID_FILE    pid file for daemon mode
      --log-file LOG_FILE    log file for daemon mode
      --user USER            username to run as
      -v, -vv                verbose mode
      -q, -qq                quiet mode, only show warnings/errors
      --version              show version information
    ```

- **配置开机启动（ubuntu 版本）**
    进入到开机启动目录
    ```bash
    $ cd /etc/init.d/
    ```
    文件夹里面添加文件
    ```bash
    $ sudo touch shadowsocks-start.sh
    ```
    ```
    $ sudo vim /etc/init.d/shadowsocks-start.sh
    ```
    内容如下（不做解释，参考insserv的两篇文章：[链接1](http://www.thegeekstuff.com/2014/08/insserv-command/) [链接2](http://manpages.ubuntu.com/manpages/wily/man8/insserv.8.html)）：
    ```text
    #! /bin/sh
    ### BEGIN INIT INFO
    # Provides:          shadowsocks
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Should-Start:      $network $time
    # Should-stop:       $network $time
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: shadowsocks.
    ### END INIT INFO

    /usr/bin/ssserver -c /etc/shadowsocks.json -d start
    ```
    设置启动等级：
    ```
    $ sudo update-rc.d /etc/init.d/shadowsocks-start.sh defaults 90
    ```
#### **以上配置好了服务器端的内容了**

#### 2. **客户端配置**
- ** 安装shadowsocks **
    和服务器端一样的安装方式，通过pip 安装或者源码安装
    ```bash
    $ pip install shadowsocks
    ```
- **配置客户端的shadowsocks.json文件**
    创建shadowsocks.json文件:
    ``` bash
    $ sudo vim /etc/shadowsocks.json
    ```
    内容如下:
    ``` text
    {
        "server":"my_server_ip",
        "server_port":8388,
        "local_port":7070,
        "password":"mypassword",
        "timeout":300,
        "method":"rc4-md5",
        "auth": true
    }
    ```
- **启动客户端shadowsocks服务**
    启动：
    ```bash
    $ sslocal -c /etc/shadowsocks.json -d start
    ```
    关闭：
    ```
    $ sslocal -c /etc/shadowsocks.json -d stop
    ```
关于sslocal 的使用可以参考 sslocal -h，这里不多描述
#### **至此以上已经配置好了shadowsocks的服务了，可是还不能正常使用，原因很简单，shadowsocks是使用的是socks5代理，如果是浏览器使用，需要安装特定的插件，当然firefox可以直接配置proxy即可，chrome 需要安装SwitchyOmega类似的插件，并且配置。这样一来就不能是全局的使用了，仅仅局限于浏览器。有很多代理转发工具，但是本文将介绍Polipo进行http/https的代理转发（ubuntu版本）**

#### 3. **polipo安装**
```bash
$ sudo apt-get install polipo
```
#### 4. ** polipo配置 **（[参考1](https://github.com/jech/polipo/blob/master/config.sample) [参考2](https://help.ubuntu.com/community/Polipo))
polipo配置方式:
```bash
$ sudo vim /etc/polipo/config
```
/etc/polipo/config 内容:
```text
# This file only needs to list configuration variables that deviate
# from the default values.  See /usr/share/doc/polipo/examples/config.sample
# and "polipo -v" for variables you can tweak and further information.

logSyslog = true
logFile = /var/log/polipo/polipo.log

proxyAddress = "0.0.0.0"
proxyPort = 17070
socksParentProxy = "127.0.0.1:7070"
socksProxyType = socks5
allowedClients = 127.0.0.1
```
#### 5. **polipo重启**
```bash
$ sudo /etc/init.d/polipo restart
```
设置http/https代理
```bash
$ export http_proxy="http://127.0.0.1:17070" && export http_proxy="http://127.0.0.1:17070"
```
取消代理
```bash
$ unset http_proxy && unset https_proxy
```

以上是配置linux(ubuntu)版本的shadowsocks全局代理
window下建议使用Proxifier, [下载地址](https://www.proxifier.com/download.htm)。其它Proxifier相关注册码信息参考[链接](http://gooday.xyz/proxifier%E6%B3%A8%E5%86%8C%E7%A0%81%E4%BB%A5%E5%8F%8A%E9%85%8D%E5%90%88shadowsocks%E4%B8%8A%E7%BD%91/)
