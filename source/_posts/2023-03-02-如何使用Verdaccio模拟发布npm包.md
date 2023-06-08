---
title: 如何使用Verdaccio模拟发布npm包
date: 2023-03-02 20:00:04
tags:
categories:
---

### 简介

在我们实际开发npm包过程中，当我们认为自己写的包已经没多大问题的时候，需要在发布前进行相关验证，这个时候有两种方案，一种是使用npm link方式，但是好像有时候不太好使。今天介绍一个利用Verdaccio进行模拟发布，然后再安装该模拟服务器上的包，进行验证

### 使用步骤

- 1.全局安装Verdaccio
- 2.终端启动Verdaccio，会在本地启动一个http://localhost:4873/服务
- 3.npm adduser –registry http://localhost:4873/ 注册一个用户到刚才的服务器上，注册的同时，会默认登陆（随便写name, password,email）
- 4.将包的.npmrc中的registry设置为https://registry.npmjs.org/（为了是将该包发布到模拟服务器上）
- 5.再其他要使用该包的项目中安装npm install 包名 --registry=http://localhost:4873/，制定安装模拟服务器下的包名
- 6.进行验证，没问题后将包中.npmrc的registry改回去
