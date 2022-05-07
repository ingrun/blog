---
title: JS 的一些常用操作
date: 2019-03-012
---



### 查询符合条件的第一个元素

```js
let s = [1,23,4]

s.find(i => i === 1)
// 1
s.find(i => i === 2)
// undefined   无符合条件
s.findIndex(i => i === 2)
// -1   无符合条件
s.some(i => i === 1)
// true
s.some(i => i === 2)
// false  无符合条件
s.findIndex(i => i === 23)
// 1   返回下标
```



### map

```js
// 遍历数组，  并将返回值组合成新数组返回
let s = [1,23,4]

s.map(e => {console.log(e); return e+10} )
1
23
4
(3) [11, 33, 14]

```



### filter

```js
// 返回符合条件的所有数据 并组成新数组
let s = [1,23,4]

s.filter( i => i > 1)
(2) [23, 4]
```



