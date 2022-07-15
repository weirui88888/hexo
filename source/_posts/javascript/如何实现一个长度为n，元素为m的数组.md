---
title: 如何实现一个长度为n，元素为m的数组
date: 2022-07-10 12:39:14
hidden: true
---

### 通过 fill 实现

```javascript
const generateArray = (n, m) => {
  const res = new Array(n)
  return res.fill(m)
}
```

### 通过 Array.from 实现

该方法很巧妙，可以将一个伪数组转变成一个真数组，最方便的是第二个参数，可以理解为一个 map

```javascript
const generateArray = (n, m) => {
  return Array.from({ length: n }, () => m)
}
```
