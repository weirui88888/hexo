---
title: github actions
date: 2022-06-09 13:47:08
tags:
  - github action
categories:
  - github
---


在实际的开发工作中，经常会听到一个词**持续集成**，我理解的持续集成可以归纳为

程序猿只用关系在具体的业务代码实现上，其他的工作都交给工具去实现，这里的**其他**两个字包括了很多，有`单元测试`、`构建`、`部署`...我们的精力应该放在开发应用中，这些辅助工具往往在项目启动阶段，就需要设计并实现，进而辅助开发工作。

在工作和学习中，接触过一些包括`jenkins`、`docker`、`nginx`的配置，但是时间长了，很多东西就忘了，而且本身对于我们前端开发就不太友好（偏运维）

这篇文章会记录下关于神器[github action](https://docs.github.com/cn)的一些运用技巧和理解

<!-- more -->

### 如何获取提供给action用的密钥

1.登录aliyun
2.从右上角头像中点击获取access key
3.登录自己的github 项目找到secrets，设置刚才获取的key和secret

### 关于github action的一些理解

可以把我们创建的yaml文件当作一个工作流程`W`，而`W`中可能又会包括一个或者多个作业`J1` `J2`...而每个作业中又可能包括多个步骤`S1` `S2` `S3`...

大致的结构层次图如下

- Work
  - job1
    - step1
    - step2
    - step3
  - job2
    - step1
    - step2
    - step3

一个job中的变量共享很容易，多个job中的变量可参考`deploy.yaml`

### 自定义环境变量语法

> github action中的secrets.GITHUB_TOKEN，可以不用手动的设置，会由action提供



