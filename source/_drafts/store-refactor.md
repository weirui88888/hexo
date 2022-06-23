---
title: store-refactor
tags:
---

## 简介

记录重构黄油商店页面所做的工作

## 步骤
1.通过vue脚手架创建vue项目，并且使用预设（带eslint、vuex、vue-router）

2.通过github新建项目，切换主分支从master到main

3.配置eslint && prettier，解决冲突

4.配置commitizen、git-cz、lint-staged、husky、commitlint

5.通过dotenv设置不同环境开发环境

6.add `passive: false` in touchmove 去优化触发事件

7.添加calculateRem、setHTMLFontSize(rem不太适合这个项目，剔除了)

- calculateRem这个方法主要是因为代码中有的需要根据变量算出宽高，通过`:style`实现行内样式，这个时候pxtorem会失效

8.配置webpack相关配置，包括生产环境cdn分离，添加构建版本号基于changelog，配置splitChunk拆包，删除prefetch、preload

9.删除lottie-web,自己css实现一个简单动画

10.删除重复的包，buttercam和buttertools几乎一模一样，删除了buttercam



