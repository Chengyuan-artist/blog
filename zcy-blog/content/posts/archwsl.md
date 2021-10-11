---
title: "WSL2下优雅使用Arch"
subtitle: ""
date: 2021-09-27T15:58:31+08:00
lastmod: 2021-09-27T15:58:31+08:00
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

记录Arch on wsl2的安装配置过程

<!--more-->

## [ArchWsl](https://github.com/yuk7/ArchWSL)下载安装

1. 运行 Arch.exe 自动安装配置
2. 一个Arch就装好了！

## Arch基本配置

> 使用`wsl -d <Arch name>`进入Arch Bash Shell

### 用户

1. 设置root密码

   ``` bash
   [root@PC-NAME user]# passwd
   ```

2. 添加用户

   ```bash
   [root@PC-NAME]# EDITOR=nano visudo
       %wheel      ALL=(ALL) ALL
       (设置 sudoers 文件 uncomment the above line)
       (保存 退出)
   
   [root@PC-NAME]# useradd -m -G wheel -s /bin/bash {username}
   (添加用户)
   
   [root@PC-NAME user]# passwd {username}
   (设置默认用户密码)
   ```

3. 设置默认用户

   ```powershell
   Arch.exe config --default-user {username}
   ```

### Pacman

1. 初始化密钥环（keyring） (必须执行此步骤才可以使用 Pacman)

   ```bash
   [user@PC-NAME]$ sudo pacman-key --init
   
   [user@PC-NAME]$ sudo pacman-key --populate
   ```

2. 修改pacman镜像源

   ```bash
   [user@PC-NAME]$ sudo vim /etc/pacman.d
   ```

   uncomment其中的China部分Server

3. 使用Pacman滚系统

   ``` bash
   [user@PC-NAME]$ sudo pacman -Syu
   ```

### 代理

`wsl2`网络与windows网络ip是分开的，这里实现代理的方法是让wsl2走win下的代理

1. Clash客户端开启LAN

2. 将

   ``` bash
   host_ip=$(cat /etc/resolv.conf |grep "nameserver" |cut -f 2 -d " ")
   export ALL_PROXY="http://$host_ip:7890"
   ```

   导入`.bashrc`或你正在使用的shell的配置文件（如`.zshrc`)

   ``` bash
   source ~/.bashrc
   ```

   >`/etc/resolv.conf` 保存了wsl的ip地址，由wsl每次运行时（猜测）自动生成



基本配置到这里就做好了。