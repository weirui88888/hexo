---
title: Ubuntu云服务器安装nginx
date: 2024-01-15 12:01:44
tags:
  - nginx
categories:
  - 后端
---


Nginx在默认的Ubuntu存储库中可用。要安装它，请[使用apt命令](https://www.myfreax.com/how-to-use-apt-command/)运行以下命令，这将会更新软件包索引并且安装Nginx。

## 前置知识

> sudo apt update

命令用于更新本地软件包列表。它会从配置的软件源（repositories）中获取最新的软件包信息，并将其存储在本地的软件包数据库中。这个命令并不会真正地安装或升级软件包，它只是更新了可用软件包的列表。这样做是为了确保您获取到最新的软件包信息，以便后续的软件包管理操作能够准确地进行

>sudo apt upgrade

命令用于升级已安装的软件包至最新可用版本。当您运行此命令时，APT会检查可用的软件包更新，并将所有可用的更新软件包进行安装。它会在安装之前显示将要升级的软件包列表，并询问您是否要继续执行升级操作

> apt list --upgradable

使用`--list`选项来查看可更新的软件包列表。具体命令是`apt list --upgradable`。运行这个命令后，它会列出所有可以升级的软件包及其版本号。



```shell
# 按照下方顺序执行
sudo apt update
sudo apt install nginx
sudo systemctl enable nginx # 开机启动
sudo ufw enable # 启用防火墙
sudo ufw allow 22/tcp # 允许22端口，这样才可以通过ssh方式登录服务器
sudo ufw allow 'Nginx Full' # 允许服务器80和443端口
```

```
sudo systemctl status nginx # 查看nginx状态
sudo systemctl reload nginx # 每次修改nginx配置文件后，重启
```

## 如何安装

```shell
sudo apt update 
sudo apt install nginx
```

[如何在Ubuntu 20.04安装Nginx](https://www.myfreax.com/how-to-install-nginx-on-ubuntu-20-04/)

[你应该知道的Nginx命令](https://www.myfreax.com/nginx-commands-you-should-know/)

[如何在Ubuntu上使用SSL来保护Nginx](https://cloud.tencent.com/developer/article/1177658)