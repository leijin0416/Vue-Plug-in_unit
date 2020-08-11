# Vue 资料整理

[Vue主动刷新页面及列表数据删除后的刷新方法](https://www.cnblogs.com/mica/tag/Vue/)

## 递归拷贝

[JS高频手写实用方法](https://www.cnblogs.com/chenwenhao/p/11294541.html)

- **`deepClone(obj[i])`** -执行递归

```js
let obj = {a: 1, b: {x: 3}}

function deepClone(obj) {
    let copy = obj instanceof Array ? [] : {}
    for (let i in obj) {
        if (obj.hasOwnProperty(i)) {
            copy[i] = typeof obj[i] === 'object' ? deepClone(obj[i]) : obj[i]    // 判断是否需要递归
        }
    }
    return copy
}
```

## sort 排序

根据数组中的对象的多个属性值排序，多条件排序；

```js
let arr6 = [{id:10,age:2},{id:5,age:4},{id:6,age:10},{id:9,age:6},{id:2,age:8},{id:10,age:9}];
let arr7 = arr6.sort( (a,b) => {
　　if (a.id === b.id) {  // 如果id相同，按照age的降序
　　　　return b.age - a.age
　　} else {
　　　　return a.id - b.id
　　}
}).map(item => item)
console.log(arr7);
```
