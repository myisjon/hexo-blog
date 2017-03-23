---
title: john-blog
tags: [Next, Hexo主题配置]
categories:
- init
---
使用hexo的默认主题没有自己喜欢感觉,因此换了另一个多功能的主题--[Next](https://github.com/iissnan/hexo-theme-next)

## Next配置
其实这个没有什么好介绍的, 就是按照官方文档clone下来的项目,会包含.git这个文件,个人觉得比较恶心,所以直接删掉了.那样我就成功的将next这个项目合并到了我blog项目里面了,不然我就得先fork Next 这个项目,拆分成一个新的项目,我只是为了写写小文章而已,不想搞得太复杂了,再者我不会改里面的代码,只是做伸手党.
因为Next这个做的相当不错了,引入第三方插件很简单,只需要配置该主题的_comfig.yml 文件即可.
**<font color="red">注意</font>**: 以下的操作是在站点的根目录下

### [引入Next 主题](http://theme-next.iissnan.com/getting-started.html)
```bash
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
**可选操作(如果需要将Next主题加入到站点的项目里,可以执行一下操作)**:
```
$ rm -rf themes/next/.git*
```

### 修改**<font color="red">站点/font>**_config.yml文件
```
theme: next
```

### 验证主题
重新执行一下
```bash
$ hexo s --debug
```
浏览器访问: http://localhost:4000 ,看到以下的效果即可:
![check_img](http://theme-next.iissnan.com/uploads/five-minutes-setup/validation-default-scheme-mac.png)

以上则是配置Next主题了
**备注**:
可以主题的第三方插件对站点做一些统计,详情请查看[Next 第三方服务](http://theme-next.iissnan.com/third-party-services.html)


