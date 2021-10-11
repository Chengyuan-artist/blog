---
title: "go get代理配置问题"
subtitle: ""
date: 2021-10-11T11:23:57+08:00
lastmod: 2021-10-11T11:23:57+08:00
draft: false
author: ""
authorLink: ""
description: ""

tags: []
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

记录下go get 配置代理的坑

<!--more-->

今天在`yay yay`时，发现`go build`时某几个包总是get不下来，遂研究半天。

发现`go get` 貌似不认`ALL_PROXY`环境变量，只认`http_proxy`和`https_proxy`, 特别是`https_proxy`要配好....

OS第一节课搞了几十分钟。。。

`git`代理好像不用配，自动继承命令行代理。