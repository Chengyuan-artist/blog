---
title: "Hugo博客搭建"
subtitle: ""
date: 2021-09-23T23:02:40+08:00
lastmod: 2021-09-24 13:53:25Z
draft: false
author: "zcy"
authorLink: ""
description: ""

tags: ["Hugo", "blog"]
categories: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

记录Hugo博客搭建的过程

<!--more-->

# 博客搭建

> 本文初步设定按照时间顺序，记录博客搭建的过程和踩过的坑，以及过程中学会的新姿势（

## 技术栈选择

> Hugo + GitHub Page

搭博客这个想法年初就有了，git仓库也建好了，结果一直咕到现在。。。

搭之前参考了一下周围同龄人大佬的技术栈，基本上分为Hexo和Hugo两类。逛了一下Hugo git仓库发现是拿go写的，果断选择Hugo（不得不说，go确实优美）。也是早早听说Github有免费托管网页的功能，因为相较其他部署方法（服务器Netlify等）感觉比较方便，所以选择了Github page。

直到暑假NUS夏令营接触了一下云服务器，开学看了计网自顶而下的应用层部分，才知道HTTP协议，网络应用通信的基本原理有多简单。后悔没有早点了解，一直云里雾里。

## Hugo安装

> Windows 11

一定要记得下载extend版本，非extend版本不支持Sass/SCSS扩展

Hugo并不提供installer安装版本，需要自行从git仓库下载对应版本安装包并解压。

对于具体的安装，[官方文档](https://gohugo.io/getting-started/installing/)还贴心地给出了面对Technical Users和Less-technical Users的不同教程（，然而在Technical Users一栏在PowerShell中配置Path的方法是临时的。好奇PowerShell有没有像Linux `.bashrc`配置文件一样的东西，经过一番google发现确实有，Ps用一个叫profile的文件管理启动配置。于是马上按[文档](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.1#how-to-create-a-profile)来创建。

创建

```powershell
if (!(Test-Path -Path $PROFILE)) {
  New-Item -ItemType File -Path $PROFILE -Force
}
```

编辑

```powershell
code $PROFILE
```

配置`.ps1`

```powershell
function InitPath {
    $env:Path += ";D:\Coding\Hugo\bin"
}
InitPath
```

> 研究了一下，Ps的`$env:path`变量保存的初值是系统path+用户path，这条语句只会改变当前ps命令行的path值，不会影响系统变量。

兴高采烈打开新Ps窗口，结果一大堆红字就跳了出来，貌似是没有脚本运行权限。又search一番发现，原来ps默认禁止配置脚本运行（晕。查阅有关[文档](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.1)，在Ps中`Get-ExecutionPolicy`的默认值为`Restricted`，不允许任何配置脚本文件运行，把此值改为`RemoteSigned`即可

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

> `RemoteSigned`默认为Windows server的权限属性
>
> ` Restricted`默认为Windows client的权限属性（安全性考虑？普通用户真就不当人呗）

至此，Hugo终于安装好了。

`ArchLinux`的安装只需要一行

```bash
sudo pacman -Syu hugo
```

泪目了

按照传统异能

```powershell
hugo version
```

显示

```
hugo v0.88.1-5BC54738+extended windows/amd64 BuildDate=2021-09-04T09:39:19Z VendorInfo=gohugoio
```

## Hugo配置

Hugo使用`config.toml`文件来进行具体配置。除了一些通用配置外，剩下的配置基本都是根据你选定的主题xjb魔改就行了。例如：

```
hugo new site blog
cd blog
```

初始化git仓库

``` 
git init
```

主题最好以submodule的形式添加，便于更新

这里在`themes`文件夹下添加了[LoveIt](https://github.com/dillonzq/LoveIt/)主题

``` 
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
```

在配置文件`config.toml`中添加

``` toml
theme = "LoveIt"
```

剩下的配置按照[LoveIt官方文档](https://hugoloveit.com/zh-cn/theme-documentation-basics/#basic-configuration)和`themes/LoveIt`目录下的`exampleSite`魔改即可。

对于文章创建，Hugo使用`Archetypes`下的模板为你生成博客文件，不同主题都有自己不同的模板，使用命令

``` 
hugo new posts/first_post.md
```

即可按照模板生成文档。

或者，

``` 
hugo new posts/first_post/index.md
```

这样便于将图片等资源放在文档同一目录下，便于索引。见Hugo文档[Leaf Bundles](https://gohugo.io/content-management/page-bundles/#leaf-bundles)

另外，在查文档时还了解到用日期年月日来形成url索引文章的方式，如 9/23/21/hello.md。有空再去了解

在配置网站的过程中，使用

``` 
hugo server -D
```

可以实时查看网站的变化

当调整满意后，使用

``` 
hugo
```

生成静态网站，默认输出在`public`目录

## 部署

一开始的想法是在Github建两个仓库，一个存网站Hugo源码，gitignore`public`文件更新；另一个以`username.github.io `命名的仓库上传`public`的内容，逻辑看起来很清晰，对吧。但一开始直接犯蠢把Hugo源码发到`username.github.io`仓库了，结果Github page部署选择没法选`public`子文件，由于git姿势不够，遂把整个库删了... 后来搞了两个仓库，又忘记ignore`public`，直接在`public`下新建git仓库，当时就感觉有点不对劲，因为这样的操作会导致`public`文件下的每个更新都会影响到整颗文件树，而本意是希望二者相对独立互不干扰（虽说加了gitignore当前情境下就没问题了，但毕竟是个一般性问题）。参考了[这篇博客](https://zhuanlan.zhihu.com/p/37752930)，发现git还有`git worktree add`操作，就是为了解决上述问题，新姿势get。

这篇博客中提出了两种部署方法

1. 将hugo输出文件的默认文件夹名称改为docs

   只需要在`config.toml`中添加

   ``` toml
   publishDir = docs
   ```

   即可

   随后可以直接修改GitHub page部署`docs`下的网页。

2. 在`public`文件夹下建立独立分支

   配置GitHub page部署该独立分支的内容

   代码如下（示例）

   ```
   //忽略public子目录
   echo "public" >> .gitignore
   //初始化publish branch
   git checkout --orphan publish
   git reset --hard
   git commit --allow-empty -m "init publish branch"
   git push origin publish
   git checkout main
   
   // 部署
   rm -rf public
   mkdir public
   rm -rf .git/worktrees/public/
   
   // 在public下建立publish独立分支pulish
   // index reset 至远端 publish
   git worktree add -B publish public origin/publish
   
   hugo
   
   cd public
   git add --all
   git commit -m "some words"
   
   git push origin publish
   ```

   这个方法的好处是可以将源文档和生成的网页文档的版本历史**分开控制**

   有关git worktree 相关知识，下篇博客总结一下（咕咕咕



发现Netlify不仅有CDN服务器，还支持git仓库自动部署，直接run去Netlify了 (逃)