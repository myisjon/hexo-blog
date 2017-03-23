---
title: hexo部署
tag: [运维, hexo, hexo安装]
categories:
- init
---
* 为了自己可以管理自己写的东西,不想受限,于是自己就捣鼓起来......
* 于是在[godaddy](https://sg.godaddy.com/)上租了一个域名:johndeng.info
* 在[bandwagonhost](https://bandwagonhost.com/)上租上了VPS, 使用google 查到了hexo,接下来就是搭建的过程了

## 第一步(了解hexo)

### 访问hexo官网 https://hexo.io/

看到简单几步安装hexo过程,如下:
```bash
$ npm install hexo-cli -g
$ hexo init blog
$ cd blog
$ npm install
$ hexo server
```
因为我没有写过node和接触其它js相关的东西,所以我电脑里面没有npm这个命令,于是遇到了第一关卡,解决npm的安装问题

## 第二步(搭建hexo环境, 以ubuntu OS 为例)
其实在hexo的文档里写了hexo开始到使用的整个过程,我仅仅作为一个搬运工而已.
文档链接: https://hexo.io/docs/
### 开始搬运
#### hexo 必要工具
1. [git](https://git-scm.com/)
2. [Node.js](https://nodejs.org/en/)
#### 安装git
依然有现成的文档: https://git-scm.com/download/linux
不过为了减少跳转麻烦(总得充填字数嘛!^_^),我还是搬运到这里了,如果遇到错误情况,请在文章下留言或者提供解决方案.
安装命令如下(没错就是一行,也不用配置):
```bash
$ sudo apt-get install git -y
```
#### 安装Node.js
说明: node.js 有各种版本,为了对node.js 版本管理, 需要安装一个nvm对node.js做版本管理
nvm的源码的链接: https://github.com/creationix/nvm
1. 安装[nvm](https://github.com/creationix/nvm#install-script):
```bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
```
2. 检查nvm是否装好(显示版本了,就没有问题了)
```bash
$ nvm --version
```
3. 安装最新的node.js
```bash
$ nvm install stable
```
4. 检查node.js
```bash
$ nvm ls
```
    结果(大概如下):
    ```
    ->       v7.7.3
             system
    default -> stable (-> v7.7.3)
    node -> stable (-> v7.7.3) (default)
    stable -> 7.7 (-> v7.7.3) (default)
    iojs -> N/A (default)
    lts/* -> lts/boron (-> N/A)
    lts/argon -> v4.8.0 (-> N/A)
    lts/boron -> v6.10.0 (-> N/A)
    ```
#### 安装hexo
```bash
npm install -g hexo-cli
```

好了,hexo环境就是这样安装好了,下面开始弄自己的blog站点了

## 第三部
### 初始化站点
```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
初始化完之后,文件结构大致如下:
```
blog
├── _config.yml
├── db.json
├── debug.log
├── node_modules
├── package.json
├── public
├── scaffolds
├── source
└── themes
```
简单介绍一下各个文件的作用
* [_config.yml](https://hexo.io/zh-cn/docs/configuration.html) 是整个站点总体配置文件,控制blog站点使用哪些扩展
* db.json 是站点的数据库信息,保存这站点相关数据,实际上很少操作这个(没有运行貌似没有这个文件)
* debug.log 是npm install 出错后的信息文件
* node_modules 是npm 生产一堆静态文件, 可以无视掉
* pakage.json 是站点的所有依赖以及构建文件
* public 是站点跟路径文件
* [scaffolds](https://hexo.io/zh-cn/docs/writing.html) 模板文件夹,想要更改模板就修改这个文件里面的内容, Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。
* source 资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
* [themes](https://hexo.io/zh-cn/docs/themes.html) 主题文件夹。Hexo 会根据主题来生成静态页面。更多主题选择参考如下:
    1. [hexo 官网的主题列表](https://hexo.io/themes/)
    2. [知乎问答](https://www.zhihu.com/question/24422335)
    3. [github 大全](https://github.com/search?o=desc&q=hexo-theme&s=stars&type=Repositories&utf8=%E2%9C%93)

好了,其实到了这步,已经可以运行了.
## 调试运行看结果
由于hexo 3.0 已经将服务器独立成了个别模块, 所以需要安装[hexo-server](https://github.com/hexojs/hexo-server) 才能使用。
```bash
$ npm install hexo-server --save
```
终于可以启动了:
``` bash
$ hexo server
```
对于hexo有很更好玩的参数,详细
More info: [Server](https://hexo.io/docs/server.html)

备注一下:
快速生成文件:
```bash
$ hexo g
```
启动服务:
```bash
$ hexo s
```


下一篇将记录hexo的next 配置使用
