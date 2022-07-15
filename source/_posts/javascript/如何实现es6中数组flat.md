---
title: 如何实现es6中数组flat
date: 2022-07-10 13:19:56
hidden: true
---

### 考察点

这个题目其实并不复杂，重要的是考察一些边界条件的判断以及递归的实现，我看到这个问题，想到的就是以下一些点

- 如何判断数组中的一项是不是数组类型
  - Array.isArray
  - Object.prototype.toString
- 参数的边界条件，数字类型，整数
- 通过原型链的方式拓展自定义\_flat 方法
- arguments.callee实现对方法的复用

下面是我的简单实现

```javascript
Array.prototype._flat = function (flag) {
  const _flat = arguments.callee // arguments.callee实现对方法的复用
  if (!Array.isArray(this)) {
    throw "this is not a array"
  }
  if (typeof flag === "undefined") { // 这里可以通过给函数参数设置默认值flag = 1实现，但是控制台中有严格模式的报错
    flag = 1
  }
  if (typeof flag !== "number") {
    throw "flag should be a number"
  }
  if (!Number.isInteger(flag) && flag !== Infinity) {
    throw "flag should be a integer"
  }
  const res =
    flag > 0
      ? this.reduce((acc, cur) => {
          if (!Array.isArray(cur)) {
            return [...acc, cur]
          } else {
            return [...acc, ..._flat.apply(cur, [flag - 1])] // 递归对数组中的元素执行该方法，使用apply冒用借充函数
          }
        }, [])
      : this
  return res
}
const arr = [1, 2, [3, [4, 5, [6, 7, [9, 10, [11, 12]]]]]]
console.log(arr._flat()) // 不传递参数默认flag = 1，结果：[1, 2, 3, [4, 5, [6, 7, [9, 10, [11, 12]]]]]
console.log(arr._flat(1)) // 同上面一样
console.log(arr._flat(2)) // 结果：[1, 2, 3, 4, 5, [6, 7, [9, 10, [11, 12]]]]
console.log(arr._flat(3)) // 结果：[1, 2, 3, 4, 5, 6, 7, [9, 10, [11, 12]]]
...
console.log(arr._flat(Infinity)) // 结果：[1, 2, 3, 4, 5, 6, 7, 9, 10, 11, 12]
```
