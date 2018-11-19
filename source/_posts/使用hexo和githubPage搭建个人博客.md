---
title: 使用hexo和github page 搭建个人博客
---

刚刚把个人博客重新搭好，感觉自己找的几个教程都有点没讲清楚，于是打算自己总结下。
首先是github page 的创建

先创建一个 名为xxx.github.io的库 xxx用你自己的用户名代替
然后进入setting界面点击Launch automatic page generator

然后一路按提示选好后，这时候访问xxx.github.io就可以看到网页了

接着就是使用hexo构建自己的网页

先安装Nodejs
```
sudo apt-get install nodejs
sudo apt-get install npm
```
然后安装hexo
```
sudo npm install hexo-cli -g
```
开始搭建
```
hexo init blog
cd blog
sudo npm install
hexo server
```
输完上面的代码就可以在本地看见博客的样子了

接着打开——config.yml文件
进行如下配置
```
# Site
title: Chubby's Blog
subtitle: android 萌新
description:
author:  Chubby
language: zh-CN
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: cjnhust.github.io
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo : https://github.com/cjnhust/cjnhust.github.io.git
  branch : master
```
这里截取了一部分要修改的并不是全部

Site
主要是进行博客标题，作者等信息的设置

URL
url和permalink这两个分别是你的站点的url host地址以及文章的永久连接
我这里设置的是个人域名，没有的话写github page地址就可以了

Deployment
这里进行的是hexo deploy之前的设定 可以让你的博客同步到github上
repo是你之前github page库的地址
branch是你page所在的分支

同步到github
一切配置好之后然后输入
```
npm install hexo-deployer-git --save
```
安装好之后输入
```
hexo d -g
```
这样就可以同步到github上了
个人域名绑定
在blog目录下的source文件夹内新建一个CNAME文件（注意不要有后缀）
然后在文件里输入自己的域名
再输入
```
hexo d -g
```
再进行dns配置，ip改成自己github page 的ip
等一会儿就可以了
这样一个个人博客就搭建好了，如果觉得默认主题丑还可以换主题
我使用的是material的主题
首先在blog目录下
```
cd themes
```
然后
```
git clone https://github.com/viosey/hexo-theme-material.git
```
接着将blog下的_config.yml文件中的主题修改成hexo-theme-material
再运行
```
hexo g -d
```
这样就可以了
ps:如果遇到Cannot read property 'startsWith' of null
修改layout/_widget/dnsprefetch.ejs文件。修改内容如下：
```
<% } else if(theme.comment.use.startsWith("disqus")) { %>
// to
<% } else if(theme.comment.use && theme.comment.use.startsWith("disqus")) { %>
```
