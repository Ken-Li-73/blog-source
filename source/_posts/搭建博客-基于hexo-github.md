---
title: 搭建博客-基于hexo-github
tags:
  - 博客搭建
  - git
  - nodejs
  - hexo
categories:
  - hexo
  - 搭建博客
comments: true
abbrlink: 29cff741
date: 2021-11-10 18:50:11
---

自己搭建一个博客，可以锻炼自己的能力， 也可以去记录一下自己的心得。本文是将资源托管于github仓库，基于hexo搭建的，下面是我自己记录的搭建过程。

1. git的安装和使用
2. nodejs的安装和使用
3. hexo的安装和使用

<!-- more -->

我使用的软件及版本如下：

```yml
git: 2.33.1.windows.1
node: 16.13.0
hexo: 5.4.0
```

## git的安装和使用

### 注册账号

如果进不去[github](https://www.github.com/)的官网，则在本地hosts的文件(C:\Windows\System32\drivers\etc)添加:

```js
//这个ip可能会变化
140.82.114.4 github.com
185.199.108.153 assets-cdn.github.com
199.232.69.194 github.global.ssl.fastly.net
```

* host的文件修改需要权限
* [IPAddress.com](https://www.ipaddress.com/)网站,查询上面三个地址ip

### 安装git。

下载网址：https://git-scm.com/

路径自定义，其他的默认安装即可。

这个注意：后面会用到。

![](git_setup_branches.jpg）

安装完成后
点击鼠标右键，出现下图图标，说明安装完成。

![](check_git.png)
打开Git Bash,输入git --version,出现版本号，则表示安装完成。

![](git_version.jpg)

### 绑定SSH

通过ssh协议生成公钥，将github和本地连接起来，完成后可以在本地通过gi命令上传文件

在git bash 里面输入 ssh-keygen -t rsa,**并敲四次回车**

```bash
ssh-keygen -t rsa
```

生成两个文件，分别为秘钥 id_rsa 和公钥 id_rsa.pub

一般在根目录的文件下，windows稍微特别，在用户文件里（C:/Users/用户名/.ssh）

![](ssh-setup.jpg)

用记事本打开 id_rsa.pub，并将其中内容全部复制

![](ssh-setup2.jpg)
打开自己的github设置
![](ssh-setup3.jpg)

选择SSH and GPG keys

![](ssh-setup4.jpg)

将公钥粘贴进去

![](ssh-setup5.jpg)
验证是否成功，我们可以通过在 Git Bash 中输入 ssh -T git@github.com 进行检验：

第一次会询问你是否继续连接，输入yes就行，然后再输一次，就正常了。

![](ssh-setup6.jpg)
新建项目

![](ssh-setup7.jpg)
项目配置如下:

![](ssh-setup8.jpg)
回到主页，点击刚建好的项目

![](ssh-setup9.jpg)

复制刚建好的项目网址
![](ssh-setup10.jpg)

在本地计算机新建一个文件夹，比如我的文件夹在(D:/MyBlog)

然后在该文件下右键打开Cit bash输入：git clone 项目网址

![](ssh-setup11.jpg)

此时已经将本地文件夹和远程仓库绑定了。

## 安装[node.js](https://nodejs.org/en/)

### 测试安装

从[node.js](https://nodejs.org/en/)官方下载安装，在`cmd`中检查是否安装成功

```cmd
node -v 
npm -v
```

### 配置路径

因为在执行例如npm install webpack -g等命令全局安装的时候，默认会将模块安装在C:\Users\用户名\AppData\Roaming路径下的npm和npm_cache中，不方便管理且占用C盘空间，所以这里配置自定义的全局模块安装目录，在node.js安装目录下新建两个文件夹 node_global和node_cache，然后在cmd命令下执行如下两个命令：
![](ssh-setup12.jpg)

```cmd
npm config set prefix "F:\Program Files\nodejs\node_global"
npm config set cache "F:\Program Files\nodejs\node_cache"
```

### 配置环境变量

在系统变量中新建一个变量名为 “NODE_PATH”
![](ssh-setup12.jpg)

编辑用户变量里的Path，将相应npm的路径改为如图所示：

![](ssh-setup13.jpg)

### 测试npm模块

在cmd中执行

```cmd
npm install webpack -g
```

执行 npm webpack -v，查看webpack是否安装成功，

## Hexo安装和使用

### 下载hexo

点开MyBlog 文件夹，鼠标右键打开 Git Bush Here，输入：

```git bash
npm install webpack -g
```

### 初始化hexo

```bash
hexo init
```

### 本地静态部署

```bash
hexo g 	#生成网站静态文件
```

在本地查看：

```bash
hexo s
```

此时框架已经基本在本地布置完成:可以在 http://localhost:4000 查看新部署的网页，ctrl+c停止运行

将Hexo部署到GitHub

用记事本打开当前文件夹下的 _config.yml 文件

并在底部写上如下代码，需要把原来的deploy删除，另外冒号后面要有空格
![](ssh-setup13.jpg)

### 安装Git部署插件

单平台部署可以不安装

```bash
npm install hexo-deployer-git --save
```

### 提交到github

```
hexo clean   #清除缓存文件和已生成的静态文件
hexo g       #生成网站静态文件
hexo d       #部署到github上的仓库
```

此时你输入你项目的网址'http://用户名.github.io, 就可以访问自己的博客了。

hexo d 是上传到github上，如果你频繁的修改文件，或者只想暂时查看一下布局，可以选择：

```bash
hexo clean&&hexo g&&hexo s       
```

通过http://localhost:4000 在本地查看

当你自己一步步搭建完成后，你会发现博客具有以下特点：

- 这是一个静态网页。
- hexo将你的项目，变成静态网页，
- github仓库托管网页，可以实现联网和资源的放置。
  另外github托管的网页，不会被爬虫检索，即在百度上搜索不到

### 参考：

[配置nodejs教程](https://blog.csdn.net/antma/article/details/86104068)

[从零开始搭建个人博客（超详细）](https://zhuanlan.zhihu.com/p/102592286)
