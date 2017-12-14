---
title: django 基本环境搭建
date: 2017-11-28 13:15:22
categories:
  - web
  - python
  - python web framework
  - django
tags:
  - web
  - python
  - django
---

  django是Python成熟的开源web框架，从2005年7月用BSD许可证下发布。采用MVT的软件设计模式，即由Model、View和Template。
  Django的目的是让复杂的网站和数据驱动的网站可以简单化的开发。Django是非常注重组件的复用性、热更新(组件可插拔)，符合敏捷开发和DRY法则(Don't Repeat Yourself)。
  本章节主要介绍django最初的运行样例.

<!-- more -->

## django 环境安装

### django 相关资源

- [django源码](https://github.com/django/django)
- [django官网(English)](https://www.djangoproject.com)
- [djangog官网文档(English)](https://docs.djangoproject.com)
- [django 学习的资料整理链接](https://github.com/zaxlct/python-django-learning)
- [django目前比较好的中文文档(django 1.8.2)](http://usyiyi.cn/translate/django_182/index.html) , 对应的[gitbook地址](https://www.gitbook.com/book/wizardforcel/django-chinese-docs-18/details): https://www.gitbook.com/book/wizardforcel/django-chinese-docs-18/details

### django 环境安装

django是支持Python2和Python3的， 所以在哪个版本下安装都是可以的。但是本文主要介绍python3环境。与你使用的操作系统 (windows/MacOS/Unix/Linux) 都没有关系，因为Python3是支持的。本文主要的操作是在Ubuntu 17.04 zesty 上操作的。

** pip 安装最新的django **

pip install django

** pip 指定安装django版本 **
pip install django=={{ version }}

例如安装django 1.8.2:
> pip install django==1.8.2

更多安装的细节可以参考[官方安装文档](https://github.com/django/django/blob/master/docs/intro/install.txt), 主要是一些依赖的安装

** 检查django是否安装上 **

``` bash
$ python -c "import django; print(django.get_version())"

1.11.6
```

这里安装的是django 1.11.6版本

当然还可以使用pip list 或者 pip freeze 列出当前django版本


## 创建django的工程

可以用IDE创建，或者手动一个一个文件创建，还有就是可以用命令行创建(前提你是已经将Python的命令配置到了系统的环境变量里面)

```bash
$ pwd

/tmp

$ django-admin startproject djangoDemo

```
注意给工程起名时，最好不要使用Python的库名，以免产生冲突。

** 检查django工程项目文件 **

```bash
$ ls /tmp/djangoDemo

djangoDemo  manage.py

```

** 运行djangoDemo **

```bash
$ cd /tmp/djangoDemo
$ python manage.py runserver
```

django 会默使用8000的端口，仅允许通过127.0.0.1这个ip运行，manage.py runserver 的参数可以修改，暂时不做介绍。

此时，你可以在浏览器输入 http://127.0.0.1:8000/ ,浏览器会展示下图:

{% asset_img django-study-01.png %}

在runserver的时候会给出警告内容如下:

> You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

但是可以暂时不管，接下来将配置好后，执行python manage.py migrate 就会消除此警告。


*接下来的文章提到的顺序：*

- 基本settings 配置
- 创建应用程序(app)
- 配置路由(url)
- 注册admin组件

