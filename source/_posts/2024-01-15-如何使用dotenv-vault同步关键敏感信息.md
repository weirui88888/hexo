
---
title: 使用dotenv-vault同步多端环境变量
date: 2024-01-15 12:01:43
tags:
  - 环境变量
categories:
  - node
---

对于一些敏感信息，比如数据库相关的账户名、密码、以及一些token相关的东西，一般在我们的应用程序中，都会需要涉及到，这些环境变量的我们一般用键值对的方式放在类似于`.env` `.env.local` `.env.development` `.env.production` `.env.production.local`等文件中，然后通过`dotenv`这样的工具注入我们的应用程序中

例如：

> .env.local（一般一些敏感的信息我们都建议放在.local结尾的文件中，因为它们不会被推送到我们的代码仓库，如github中）

```javascript
DATABASE_USER_NAME=XXXX
DATABASE_USER_PASSWORD=YYYY
EXPRESS_SERVER_PORT=3001
```

这样配置之后，我们在我们的`nodejs`脚本中，通过使用`dotenv`这样的工具注入后，我们就可以在应用程序中使用它们了

```javascript
// app.js
// 这里在开发环境下，也就是我们本地的环境时，我们引入.env.local中的相关环境变量
require('dotenv').config({ path: './.env.local' }) 

// 可以获取到注入的环境变量
console.log(process.env.DATABASE_USER_NAME) // XXXX
console.log(process.env.DATABASE_USER_PASSWORD)// YYYY
console.log(process.env.NODE_SERVER_PORT) // 3001
```

但是这里其实有个问题，当我的代码，不在本地开发启动，比如想要在云主机或者其他环境中执行node app.js文件的时候，因为我们的.env.local文件并不会推送到github上，所以即使我拉取代码执行后，是找不到.env.local文件的

对于上面这个问题，有两个解决方案

- 在云主机中写一份同样的.env.local文件，虽然可行，但是效率很低，因为我们需要同步本地和云主机的这两个文件
- 我将注入变量的方式改成require('dotenv').config({ path: './.env })，这个意思就是环境变量从.env中获取，但是这又暴露出来另外一个问题，因为我们的.env文件是会随着我们的推送，更新到远端，比如github中的，相当于这些敏感信息是暴露的，显然这种方式是不合理的。

其实除了这个问题，还有一个就是，可能我会在不同环境下，需要不同的环境变量，很显然，我们通过以下一行代码，是无法完成根据不同环境，注入不同的环境变量的。

```javascript
require('dotenv').config({ path: './.env.local' })
```



那么问题来了，有没有什么方案能够解决这几个令人头疼的问题呢，看了下dotenv文档，发现它提及到了一个叫做dotenv-vault这样的一个工具，研究了一番，发现它很好的解决了这样两个问题

1.xxx

2.xxx



如何使用

1.先去登录注册

2.在需要使用的项目中，执行

npx dotenv-vault@latest new // 创建一个新的项目，然后会在项目中生成.env.vault这样一个文件，主要是用来记录相关的项目信息（也就是我们自己的项目和dotenv-vault项目之间相关的映射）
npx dotenv-vault@latest login // 登录dotenv-vault项目，我们从而可以配置环境变量，会在本地项目中生成.env.me这样一个文件，代表你已经登录了，后续才可以执行各种命令

npx dotenv-vault@latest open // 打开我们创建的这样项目，进行相关环境变量的配置，相当于可视化页面的配置方式吧（更推荐通过以下命令行进行配置环境变量，因为可视化的方式，只能一个个的添加key和value，比较慢）

3.通过脚本的方式配置当前项目的配置变量，配置的变量之后，可以打开dotenv-vault项目，立马就可以看到已经同步了

npx dotenv-vault@latest push development .env.development.local 推送本地.env.development.local中的环境变量到dotenv-vault中，在配置完成后，可以通过以下命令查看我们本地开发环境变量确实已经同步到dotenv-vault了

```shell
npx dotenv-vault@latest open development
```

npx dotenv-vault@latest push production .env.production.local

同上，目的是将生产的环境变量同步到dotenv-vault中，同理

可通过npx dotenv-vault@latest open production确认已经同步远端



接着执行npx dotenv-vault@latest build 目的构建加密的.env.vault文件，这里我也不太明白它是什么意思，官方对于这行命令的解释翻译过来是：“首先构建项目的加密 .env.vault 文件。它在与云无关的有效负载中安全地加密您的秘密。”

然后你就可以把.env.vault文件推送到GitHub这些代码仓库中了



接下来，我们应该怎么注入不同环境的变量到我们程序中呢，还是用上面的简单例子演示

假如我package.json中有这样一段代码,代表我在不同环境下，我想启动不同端口的服务

```javascript
script:{
  "start:dev":"node app.js"
  "start:production": "node app.js"
}
```



```javascript
require('dotenv').config() 

console.log(process.env.NODE_SERVER_PORT) 
```

当我执行npm run start:dev时，我希望启动的node服务端口是3001，而执行npm run start:production时，端口是3002，当然可能你会说，那我直接给他们注入对应的端口不就行了如下所示

```javascript
script:{
  "start:dev":"PORT=3001 node app.js"
  "start:production": "PORT=3002 node app.js"
}
```

虽然可以，但是你不要忘了，这些其实都不算明感信息，敏感信息比如数据库账号和密码以及一些其他的token呢，你能通过这种方式明文注入吗，显然不行。

而且在这里，它也没有实现出不同环境使用不同env文件的需求



所以看下dotenv-vault它怎么做的，

刚才我们不是已经同步本地的.env.development.local和.env.production.local到dotenv-vault中了吗，我们只需要执行以下命令，会生成一个地址，这个地址就是可以用来安全解码不同环境的环境变量的一个特殊的变量值



```bash
// 生成dotenv://:xxxxx@dotenv.org/vault/.env.vault?environment=development这样的一个变量值，可以看到后面跟着environment=development，代表着这个变量是用来解码开发环境的环境变量的
npx dotenv-vault@latest keys development 

// 生成dotenv://:xxxxx@dotenv.org/vault/.env.vault?environment=production这样的一个变量值，可以看到后面跟着environment=production，代表着这个变量是用来解码开发环境的环境变量的
npx dotenv-vault@latest keys production
```

接着我们只用在上面的例子上稍做修改

```javascript
script:{
  "start:dev":"DOTENV_KEY=dotenv://:xxx@dotenv.org/vault/.env.vault?environment=development node app.js"
  "start:production": "DOTENV_KEY=dotenv://:xxx@dotenv.org/vault/.env.vault?environment=production node app.js"
}
```

这样，当我们执行start:dev时，就会把我们这个项目的开发环境变量入住我们的node程序中，对应的当我们执行start:production时，就会把我们这个项目的生产环境变量入住我们的node程序中，当然这些环境变量中，包含任意你程序中需要的环境变量，比如上面的PORT



### 修改环境变量、同步环境变量

在这之后，我们想要添加或者修改环境变量，我们只需要在本地项目和dotenv-vault对应项目的环境变量中进行配置同步即可，举个例子，我想在dotenv-vault项目中，修改开发环境下的node服务为3009，那有两种方式，

第一种：在dotenv-vault中修改，在是我去dotenv-vault中将development下的PORT修改为3009,然后执行npx dotenv-vault@latest pull development .env.development.local去同步下远端的最新的环境变量，再执行npm run start:development时发现端口已经变成了3009

同步和修改生产环境变量，和上述类似，只是在执行不同命令时指定环境为production即可



第二种：在本地修改后，想要同步远端

比如修改本地.env.development.production中的port为3100后，我们需要先执行npx dotenv-vault@latest push production .env.production.local去将本地修改同步到远端，这个时候，再执行npm run start:production时，你会发现打印的就是最新更改后的3100了



上述命令执行都会使得本地的.env.vault文件发生变更，记得将它跟着代码提交到代码仓库中（不要害怕提交它，正式它的变化才让我们可以在项目中获取最新的环境变量）



