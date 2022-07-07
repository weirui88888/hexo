---
title: hard-source-webpack-plugin
date: 2022-07-07 16:54:43
tags:
  - 性能优化
categories:
  - 性能优化
---

### 背景

最近在重构一些旧的vue项目，项目体积一般。热更新和冷启动都还算比较ok，但是为了追求更加快的开发体验，去搜了一下，发现了这样一个webpack插件hard-source-webpack-plugin，它的作用类似于cache-loader。而vue脚手架构建的vue项目，默认已经帮我们对一些耗时较多的loader开启了cache缓存，比如babel-loader和vue-loader。你可以通过以下命令确认

```javascript
vue inspect --mode development --rule vue // 会看到vue-loader的前面配置了cache-loader
vue inspect --mode development --rule js // 会看到babel-loader的前面配置了cache-loader
```

### hard-source-webpack-plugin
在项目实现的过程中，想在代码更改的同时，查看效果的改变，而这个时候长时间的编译等待，造成了额外的时间浪费，尤其是项目体积比较大的工程，用户受影响的程度会越大。hard-source-webpack-plugin这个插件的主要作用就是基于用户每一次的编译，生成对应的中间缓存。下一次在编译的时候，再优先从缓存中获取。从而减少编译时间。我尝试在项目中安装使用了下，发现确实能够提升不少，下面是一张对比图

![hard-source-webpack-plugin](https://show.newarray.vip/blog/hard-source-webpack-plugin.png)

通过对比，发现还是能够带来不少速度提升的。


