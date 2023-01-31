---
title: 前端关于apply、call、bind的一些理解
date: 2022-08-26 21:00:57
---

## 引言

上一篇关于《面试官为啥总是喜欢问前端路由实现方式》的文章发布后，发现还是挺受欢迎的。这就给我造成了一定的困惑

> 之前花了很长时间，实现了一个自认为创意还不错的关于前端如何利用node+canvas实现一键解析博客中关键词后生成一张云图，并支持一键上传github或oss的小工具，类似于图床的功能，只不过场景是解析markdown中关键字。本想着借这个实现，让大家对node全局包有一个更加深刻的印象，同时也可以借鉴其思路解决工作中的一些特定场景下的低效问题。所以写了长篇大论，沾沾自喜的窃以为能够收获大批的认同与讨论，结果却石沉大海...

于是我就在反思，到底是哪一步出现了问题。后来，我想通了，其实就是**温饱思“婬欲”**

这句话是什么意思呢，通俗的讲就是**我只有先填饱肚子后，再会去追求精神层面的自由**。那么技术也是一样的，我如果连前端的基础知识都无法理解，再深奥的技术于我而言，又有何意义呢，我又何德何能能够去读懂源码呢？

看到这之后，我想你也应该知道了，为啥面试官，总是让你手撕代码吧。你说你工作了几年，精通各种技术，结果连最基础的如何实现apply、call、bind都被问得哑口无言，实在难以面对江东父老。

本篇文章，就是以最通俗的话，带你领略javascript语言的美，下文中的实现，**主要关注点在如何实现上，并不会处理大量的边界条件，不要吹毛求疵**。

## 自鉴

在开始正篇之前，我需要你花一分钟时间，问自己两个问题

1.你是否不折不扣的理解了javascript中关于this的指向

2.是否熟悉ES6，本文中不会用那些老掉牙的代码（并不代表你不需要了解，比如eval执行字符串代码）

**如果你做不到，那我只能说先劝你去了解下它，否则即使我说的再通俗，你也会觉得云里雾里，甚至还会喷我说的什么玩意。** 等你可以胸有成竹地告诉我this is so easy的时候，再回过头来看这篇文章，一定会有所收获。

> 任何技术都是相通的，也都是有所牵制的，学习的过程中，一定是痛苦的，因为我们会发现自己的无知。

## 正文

扯了这么多，接下来让我们开始正式**手撕**

首先我们要想实现一样东西，最快的途径就是**模仿**，我先看看你大概是个什么东西，然后是怎么用的。我照着你实现，还不手到擒来。

### call

```javascript
const mbs = {
  name: '麻不烧',
  say(prefix, age) {
    console.log(`${prefix},my name is ${this.name},i am ${age} year old`)
  }
}
```

上面我们定义了一个对象，对象中有个say方法，调用该对象上的方法后，我们得到了

```javascript
mbs.say('hello',12) // 'hello,my name is 麻不烧,i am 12 year old'
```

这个时候问题来了，如果还有另外一个对象A，也想实现上面对象中的方法say，有几种途径呢，很快我们能想到两种

- 在A对象中也照搬不误的实现一个一模一样的say方法
- 能不能借用一下上面对象中的方法say

如果你选择了第一种，那你可以出门左转了。但是如果你选择了第二种，又会面临另外一个问题，因为方法中涉及到this指向的问题，而在上面，我就特意提出了理解this指向的前置条件。能不能做到把mbs上面的say方法，借A用的同时，this指向也自然而然的指向A呢？

其实上面这段话已经很好地道出了call的真正作用，改变函数的作用域。这里先说一下，不管是call,还是apply都是**冒用借充函数**。

```javascript
const mbs = {
  name: '麻不烧',
  say(prefix, age) {
    console.log(`${prefix},my name is ${this.name},i am ${age} year old`)
  }
}

const A = {
  name:'小丁'
}

mbs.say.call(A,'hello',3) // 'hello,my name is 小丁,i am 3 year old'
```

通过以上代码片段，我们可以总结以下几点

- A中确实没有再次定义一个重复的方法，并且say方法中的this指向确实指向了A
- call方法，可以接受任意多个参数，但是要求，第一个参数必须是待被指向的对象（A），剩下的参数，都传入借过来使用的函数（say）中

既然都已经知道了call是这么个玩意，那么我们就开始来模仿实现以上两点，但模仿前，又有两个前置条件需了解

- 不管是引用数据类型还是基本数据类型，它们的方法，都是定义在原型对象上面的
- 方法中的this指向谁调用这个方法

### 开撕

先写个雏形，该自定义call方法接受N个参数，其中第一个参数是即将借用这个函数的对象，剩下的参数用rest参数表示，这就模仿出了上面的第二点的前半部分

```javascript
Function.prototype.myCall = function(target,...args){
  
}
```

我们都知道一个普通函数中的this是指向调用这个函数的对象的，那么我们想让上方say方法中的this指向调用该方法的对象，该怎么做呢？很简单，我在你这个对象上添加一个方法，当我们调用这个对象上的这个方法时，方法中的this自然就指向该对象喽

```javascript
Function.prototype.myCall = function(target,...args){
  const symbolKey = Symbol()
  target[symbolKey] = this
}
```

这里我们做了两件事，首先就是给传入的第一个对象，添加了一个key，这里用symbolKey而不随便定义另外一个key名是因为，我随意添加的名字，可能target对象上面正好有呢？这不是扯犊子呢吗...

而Symbol就是ES6中实现的，用来解决这种问题。

其次，我们为这个属性，赋了一个值this，而这个this就正是借过来使用的函数，这样我们执行该函数时，其中的this，自然而然的就指向了target。到这里，已经模仿出了上面的第一点

但是javascript要求，当我们target传入的是一个非真值的对象时，target指向window，这很好办

```javascript
Function.prototype.myCall = function(target,...args){
  target = target || window
  const symbolKey = Symbol()
  target[symbolKey] = this
}
```

我们已经给target对象上添加了方法，但是什么时候调用呢？调用的时候传入什么参数呢？这也很容易

```javascript
Function.prototype.myCall = function(target,...args){
  target = target || window
  const symbolKey = Symbol()
  target[symbolKey] = this
  target[symbolKey](...args) // args本身是rest参数，搭配的变量是一个数组，数组解构后就可以一个个传入函数中
}
```

到这里，我们已经完全实现了上面提出的两点需要模仿实现的点，但是我们的目的是把别的方法，拿过来用用，用完了之后，肯定还是要删掉的。同时这里要**感谢**评论区的同学的提醒，如果函数具备返回值的话，我们还是需要将返回值进行返回的。

终结版代码

```javascript
Function.prototype.myCall = function(target,...args){
  target = target || window
  const symbolKey = Symbol()
  target[symbolKey] = this
  const res = target[symbolKey](...args) // args本身是rest参数，搭配的变量是一个数组，数组解构后就可以一个个传入函数中
  delete target[symbolKey] // 执行完借用的函数后，删除掉，留着过年吗？
  return res
}
```

是不是很简单，哪有那么复杂？本质上，就是在借用的对象上面添加一个方法，然后执行这个方法即可，最后执行完了删除掉...

理解了call的实现，apply就很好理解了，因为本质上它们只是在使用方式上有区别而已，call调用时，从第二个参数开始，是一个个传递进去的，apply调用的时候，第二个参数是个数组而已。


### apply

```javascript
Function.prototype.myApply = function(target,args){ // 区别就是这里第二个参数直接就是个数组
  target = target || window
  const symbolKey = Symbol()
  target[symbolKey] = this
  const res = target[symbolKey](...args) // args本身是个数组，所以我们需要解构后一个个传入函数中
  delete target[symbolKey] // 执行完借用的函数后，删除掉，留着过年吗？
  return res
}
```

### bind

据说实现bind，才是那些“恐怖”的面试官经常希望我们面对面手撕的。但是有了上面的铺垫，我已经一点的不紧张了，反手来一杯卡布基诺～

还是上面那种模式，我先把bind是怎么用的，是一个什么样的形式写出来，照着模仿就行了，不了解该方法的，可以先去看下[函数绑定之bind](https://zh.javascript.info/bind)

这里我先写了一个基础版

```javascript
const mbs = {
  name: '麻不烧',
  say() {
    console.log(`my name is ${this.name}`)
  }
}

mbs.say() // 'my name is 麻不烧'

const B = {
  name: '小丁丁'
}

const sayB = mbs.say.bind(B)

sayB() // 'my name is 小丁丁'
```

提炼一下，看看bind到底是个什么玩意

- bind本身是个方法，返回值也是个方法，一般调用bind方法的也是个方法...别懵
- 接受的第一个参数是一个对象，哪个方法调用bind方法，那么这个方法中的this，就是指向这个对象

### 开撕

先写个基础架子，完成上面的第一个要素。读到这里，默认上文中的表述你都理解了，如果你感到懵逼，请从头再看一遍～

```javascript
Function.prototype.myBind = function (target) {
  target = target || {} // 处理边界条件
  return function () {} // 返回一个函数
}
```

想要完成上面提到的第二个要素，还是和实现apply与call那样，给该target添加一个方法，这样方法中的this，就是指向该target

```javascript
Function.prototype.myBind = function (target) {
  target = target || {} // 处理边界条件
  const symbolKey = Symbol()
  target[symbolKey] = this
  return function () { // 返回一个函数
    target[symbolKey]()
    delete target[symbolKey]
  } 
}
```

到这里，已经完成了bind的大部分逻辑，但是在执行bind的时候，是可以传入参数的，稍微改下上面的例子

```javascript
const mbs = {
  name: '麻不烧',
  say(prefix, age) {
    console.log(`${prefix},my name is ${this.name},i am ${age} year old`)
  }
}

mbs.say('hello',12) // 'hello,my name is 麻不烧,i am 12 year old'

const B = {
  name: '小丁丁'
}

const sayB = mbs.say.bind(B,'hello')

sayB(3) // 'hello,my name is 小丁丁,i am 3 year old''
```

这里，我们发现一个有意思的地方，不管是bind中传递的参数，还是调用bind的返回函数时传入的参数，都老老实实的传递到say方法中，其实很容易实现啦～

```javascript
Function.prototype.myBind = function (target,...outArgs) {
  target = target || {} // 处理边界条件
  const symbolKey = Symbol()
  target[symbolKey] = this
  return function (...innerArgs) { // 返回一个函数
    const res = target[symbolKey](...outArgs, ...innerArgs) // outArgs和innerArgs都是一个数组，解构后传入函数
    // delete target[symbolKey] 这里千万不能销毁绑定的函数，否则第二次调用的时候，就会出现问题。
    return res
  } 
}
```
这里要感谢评论区同学指出的问题，由于myBind返回的函数会被多次调用，所以不应该在调用一次之后，就将里面的`target[symbolKey]`给销毁掉。因为这样会导致第二次调用的时候，出现问题。

我搜了下，对于这个实现的定义是，然后我们看下它有什么意义？

> 它被称为偏函数应用程序 —— 我们通过绑定先有函数的一些参数来创建一个新函数。

假设我们想要实现两个函数，分别是对传入的数进行翻倍和翻三倍，第一时间，我们肯定想着写两个函数

```javascript
const double = n => n * 2
const double2 = double(2) // 4
const double4 = double(4) // 8
...
const triple = n => n * 3
const triple2 = triple(2) // 6
const triple4 = triple(4) // 12
...
```

确实没毛病，很容易吗。我们再用偏函数的概念去实现下

```javascript
const base = (n,m) => n * m

const double = base.bind(null,2)
const double2 = double(2) // 4
const double4 = double(4) // 8
...

const triple = base.bind(null,3)
const triple2 = triple(2) // 6
const triple4 = triple(4) // 12
...
```

看到这里，你可能有点懵逼，两者之间有啥区别呢？确实，从这个例子中，我们看不出来偏函数的优势，但是我们这只是一个简单的例子，换句话说，如果我们的base函数中，处理了大量的逻辑。如果用上面的思路，难道要重复实现两遍？

而如果用下面偏函数的实现，我们只用在base中，处理一遍即可，这就是优势～

怎么感觉有点类似于React中的高阶组件和Render Props的感觉呢...

## 总结

到这里，关于三者，我们都已经可以信手拈来了。但是说实话，在面试那种紧张的情况下，我可能还是手撕不出来。但是当我被要求被手撕之前，我一定会先问一问可爱的面试官：“我可不可以先写下它们的基础用法，这样我才能照着葫芦画出瓢”。我想，没有一个面试官，会拒绝这样一个合理的要求吧。

最后，想说一下，本来我是不打算写这篇文章的。因为确实相对来说，比较基础。网上也有成篇文章讲解，那是什么原因促使我落笔的呢？我想是以下几点：

- 一万个读者有一万个哈姆雷特，每个人对于一项技术，都会有自己的见解
- 如果我的文章可以帮助更多的读者，是我所愿意看到的，也是每一个写博客人的初衷之一
- 锻炼文笔，形成自己的风格，以后卷不动了就去做个培训老师，笔名都想好了，就叫做麻老师，不叫苍老师

