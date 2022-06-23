---
title: Serial Promises
date: 2022-06-23 22:36:01
tags:
  - javascript
  - 面试
---

并行 Promise 在 es6 中可以通过 Promise.all 实现，但是有一种场景是要串行 Promise...

## 方案 1

利用数组的 reduce 实现，主要是利用了 reduce 的机制，在实际开发工作中，经常用到 reduce 方法，很方便

```javascript
const logName = (time, name) => () =>
  new Promise((resolve) => {
    setTimeout(() => {
      console.log(name)
      resolve()
    }, time * 1000)
  })

const serialPromise = (promises) => {
  promises.reduce((prevPromise, nextPromise) => {
    return prevPromise.then(() => nextPromise())
  }, Promise.resolve())
}

serialPromise([logName(1, "小明"), logName(2, "小红"), logName(3, "小丁")])
```

## 方案 2

通过 async/await 实现

```javascript
const logName = (time, name) => () =>
  new Promise((resolve) => {
    setTimeout(() => {
      console.log(name)
      resolve()
    }, time * 1000)
  })

const serialPromise = async (promises) => {
  for (let promiseFn of promises) {
    await promiseFn()
  }
}

serialPromise([logName(1, "小明"), logName(2, "小红"), logName(3, "小丁")])
```
