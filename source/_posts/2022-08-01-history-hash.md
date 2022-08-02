---
title: 关于前端hash和history两种路由的理解与实现
date: 2022-08-01 16:25:37
tags:
   - 路由
   - 面试
   - hash
   - history
categories:
   - 原理与实践
---

![](https://show.newarray.vip/blog/route-cover.png)

### 背景
从事前端开发的同学，在实际工作中，一定会接触过**路由**这个概念，同时在面试的时候经常被问到关于前端路由的实现，面试官到底想考察什么？在现在SPA单页面模式盛行，前后端分离的背景下，我们要弄清楚路由到底是个什么玩意，它可以帮助我们加深对于前端项目线上运作的理解。

而现在我们常见的路由实现方式，主要有两种，分别是history和hash模式。

<!-- more -->

### 理解

如何理解**路由**的概念，其实还是要从**单页面**着手，进行剖析。

单页面，说白了，就是指我们的服务只有一个index.html静态文件，这个静态文件前端生成后，丢到服务器上面。用户访问的时候，就是访问这个静态页面。而静态页面中所呈现出来的所有交互，包括点击跳转，数据渲染等，都是在这个唯一的页面中完成的。

这里我用一张常见的单页应用图，可能更方便我们理解。

<img src="https://show.newarray.vip/blog/history_hash_1.png" alt="image-20220801175438673" style="zoom:33%;" />

这个例子中，可以看到有四个模块，分别是首页、商城、购物车、我的。点击每个模块，内容区域都展示对应的内容，并且浏览器在点击的时候并没有发出实际的http请求（不包括其他静态资源，只是针对页面层面），只有第一次发出的请求获得的静态文件index.html。那么问题来了，内容区域是如何认识到用户点击了不同的模块，从而更新内容区域，并且做到不需要再次发出请求呢？

这个其实就是路由做的工作：

> 通过一定的机制，监听用户的行为动作，从而做出对应的变化。

下面，我会就目前常用的两种实现路由方式进行总结和归纳，这里不区分技术栈，本身它们就是一种底层实现。

### hash模式

我们都知道一个URL是由很多部分组成，包括协议、域名、路径、query、hash等，比如上面的例子，我们点击不同模块的时候可能看到是这样的URL

- 首页：https://yourdomain.xxx.com/index.html/#/
- 商城：https://yourdomain.xxx.com/index.html/#/shop
- 购物车：https://yourdomain.xxx.com/index.html/#/shopping-cart
- 我的：https://yourdomain.xxx.com/index.html/#/main

#号后面的，就是一个URL中关于hash的组成部分，可以看到，不同路由对应的hash是不一样的，但是它们都是在访问同一个静态资源index.html。我们要做的，就是如何能够监听到URL中关于hash部分发生的变化，从而做出对应的改变。

其实浏览器已经暴露给我们一个现成的方法**hashchange**，在hash改变的时候，触发该事件。有了监听事件，且改变hash页面并不刷新，这样我们就可以在监听事件的回调函数中，执行我们展示和隐藏不同UI显示的功能，从而实现前端路由。

下面是关于hash路由的核心实现，可以看出来，主要就是监听hash的变化，渲染不同的组件代码，代码可以看[这里](https://codesandbox.io/s/shou-xie-hashlu-you-shi-xian-scfxbk)

![](https://show.newarray.vip/blog/hash.png?a=b)

从上面的一些描述和实践，我们可以得出以下结论：

- hash模式所有的工作都是在前端完成的，不需要后端服务的配合
- hash模式的实现方式就是通过监听URL中hash部分的变化，从而做出对应的渲染逻辑
- hash模式下，URL中会带有#，看起来不太美观

接下来我们看下history路由是如何实现的

### history模式

history路由模式的实现，是要归功于HTML5提供的一个history全局对象，可以将它理解为其中包含了关于我们访问网页（历史会话）的一些信息。同时它还暴露了一些有用的方法，比如：

- window.history.go 可以跳转到浏览器会话历史中的指定的某一个记录页
- window.history.forward 指向浏览器会话历史中的下一页，跟浏览器的前进按钮相同
- window.history.back 返回浏览器会话历史中的上一页，跟浏览器的回退按钮功能相同
- window.history.pushState 可以将给定的数据压入到浏览器会话历史栈中
- window.history.replaceState 将当前的会话页面的url替换成指定的数据

而history路由的实现，主要就是依靠于pushState与replaceState实现的，这里我们先总结下它们的一些特点

- 都会改变当前页面显示的url，但都不会刷新页面
- pushState是压入浏览器的会话历史栈中，会使得history.length加1，而replaceState是替换当前的这条会话历史，因此不会增加history.length

既然已经能够通过pushState或replaceState实现改变URL而不刷新页面，那么是不是如果我们能够监听到改变URL这个动作，就可以实现前端渲染逻辑的处理呢？这个时候，我们还要了解一个事件处理程序popstate，先看下它的官方定义

> 每当激活同一文档中不同的历史记录条目时，`popstate` 事件就会在对应的 `window` 对象上触发。如果当前处于激活状态的历史记录条目是由 `history.pushState()` 方法创建的或者是由 `history.replaceState()` 方法修改的，则 `popstate` 事件的 `state` 属性包含了这个历史记录条目的 `state` 对象的一个拷贝。
>
> 调用 `history.pushState()` 或者 `history.replaceState()` 不会触发 `popstate` 事件。`popstate` 事件只会在浏览器某些行为下触发，比如点击后退按钮（或者在 JavaScript 中调用 `history.back()` 方法）。即，在同一文档的两个历史记录条目之间导航会触发该事件。

 这里我用大白话总结下就是以下几点

- history.pushState和history.replaceState方法是不会触发popstate事件的
- 但是浏览器的某些行为会导致popstate，比如go、back、forward
- popstate事件对象中的state属性，可以理解是我们在通过history.pushState或history.replaceState方法时，传入的指定的数据

说了一大堆，结果却是popstate无法监听history.pushState和history.replaceState方法，这不是扯呢吗？那好吧，既然你厂商没实现此功能，那么我自己重新写下你这个history.pushState和history.replaceState方法吧，让你在这个方法中，也能够暴露出自定义的全局事件，然后我再监听自定义的事件，不就行了？

#### 改写

```javascript
let _wr = function(type) {
   let orig = history[type]
   return function() {
      let rv = orig.apply(this, arguments)
      let e = new Event(type)
      e.arguments = arguments
      window.dispatchEvent(e)
      return rv
   }
}

 history.pushState = _wr('pushState')
 history.replaceState = _wr('replaceState')
```

执行完上面两个方法后，相当于将pushState和replaceState这两个监听器注册到了window上面，具体的定义可参考[EventTarget.dispatchEvent](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/dispatchEvent)

#### 简易实现

![](https://show.newarray.vip/blog/history.png)

#### 重点

hash模式是不需要后端服务配合的。但是history模式下，如果你再跳转路由后再次刷新会得到404的错误，这个错误说白了就是浏览器会把整个地址当成一个可访问的静态资源路径进行访问，然后服务端并没有这个文件～看下面例子更好理解

##### 没刷新时，只是通过pushState改变URL，不刷新页面

```tex
http://192.168.30.161:5500/ === http://192.168.30.161:5500/index.html // 默认访问路径下的index.html文件，没毛病
http://192.168.30.161:5500/home === http://192.168.30.161:5500/index.html // 仍然访问路径下的index.html文件，没毛病
...
http://192.168.30.161:5500/mine === http://192.168.30.161:5500/index.html // 所有的路由都是访问路径下的index.html，没毛病
```

##### 一旦在某个路由下刷新页面的时候，想当于去该路径下寻找可访问的静态资源index.html，无果，报错

```tex
http://192.168.30.161:5500/mine === http://192.168.30.161:5500/mine/index.html文件，出问题了，服务器上并没有这个资源，404😭
```

**所以一般情况下，我们都需要配置下nginx，告诉服务器，当我们访问的路径资源不存在的时候，默认指向静态资源index.html**

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

### 总结

- 一般路由实现主要有history和hash两种方式
- hash的实现全部在前端，不需要后端服务器配合，兼容性好，主要是通过监听hashchange事件，处理前端业务逻辑
- history的实现，需要服务器做以下简单的配置，通过监听pushState及replaceState事件，处理前端业务逻辑

所以看到这，当面试官问你路由实现时，你还会紧张吗？

### 参考

[History 对象 - JavaScript 教程](https://wangdoc.com/javascript/bom/history.html#historybackhistoryforwardhistorygo)

[js使用dispatchEvent派发自定义事件](https://juejin.cn/post/6844903833227771917)

[单页面应用history路由实现原理](https://cloud.tencent.com/developer/article/1653836?page=1)

[在单页应用中，如何优雅的监听url的变化](https://github.com/forthealllight/blog/issues/37)

[vue路由模式及 history 模式下服务端配置](https://icode.best/i/13730847328933)

