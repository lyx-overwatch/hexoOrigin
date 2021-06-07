---
title: Array.reduce用法
date: 2020-09-23 11:29:58
categories:
- es6
---
#### reduce作用

<p>reduce() 方法对数组中的每个元素执行一个由您提供的reducer函数(升序执行)，<b>将其结果汇总为单个返回值。</b></p>

<p>reduce为数组中的每一个元素依次执行callback函数，不包括数组中被删除或从未被赋值的元素，接受四个参数：</p>

1. accumulator 累计器,累计回调的返回值; 它是上一次调用回调时返回的累积值
2. currentValue 数组中正在处理的元素。
3. currentIndex 数组中正在处理的当前元素的索引。
4. array 调用reduce()的数组

```js
// 扁平化数组
let getOneDimensionalArray = arr => {
  return arr.reduce((pre, cur) => {
    return Array.isArray(cur)
      ? next.every(item => !Array.isArray(item))
        ? pre.concat(cur)
        : pre.concat(getOneDimensionalArray(cur))
      : pre.concat(cur)
  }, [])
}
console.log(getOneDimensionalArray([[1, 2, 3, [4, [3, [2]]]], [2], [3]]))
```
