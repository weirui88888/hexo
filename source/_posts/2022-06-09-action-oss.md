---
title: github actions
date: 2022-06-09 13:47:08
tags:
categories:
---

##### 如何获取提供给action用的密钥

1.登录aliyun
2.从右上角头像中点击获取access key
3.登录自己的github 项目找到secrets，设置刚才获取的key和secret

##### 关于github action的一些理解

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

> github action中的secrets.GITHUB_TOKEN，可以不用手动的设置，会由action提供



