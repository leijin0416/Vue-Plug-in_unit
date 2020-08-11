# Vue 资料整理

[Vue主动刷新页面及列表数据删除后的刷新方法](https://www.cnblogs.com/mica/tag/Vue/) --|-- [查API兼容](https://www.caniuse.com/#search=CSS%20Variables)

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

## 数组去重

[几种常见方法](https://blog.csdn.net/m0_37885651/article/details/79738780)

```js
function uniq(array){
    var temp = [];
    var index = [];
    var l = array.length;
    for(var i = 0; i < l; i++) {
        for(var j = i + 1; j < l; j++){
            if (array[i] === array[j]){
                i++;
                j = i;
            }
        }
        temp.push(array[i]);
        index.push(i);
    }
    console.log(index);
    return temp;
}

var aa = [1,2,2,3,5,3,6,5];
console.log(uniq(aa));
```

## sort 排序

[sort()排序用法](https://www.cnblogs.com/hao-1234-1234/p/11156272.html)

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

---------------------

// 不传参数，将不会按照数值大小排序，【按照字符编码】的顺序进行排序；
var arr = ['General','Tom','Bob','John','Army'];
var resArr = arr.sort();
console.log(resArr);//输出 ["Army", "Bob", "General", "John", "Tom"]

---------------------

// 截取子串
var str = "abcdefg123456";
var res1 = str.substring(3);   // defg123456
var res2 = str.substring(3,6); // def
// 字符串大小写转换
var str = "ABCDefg123456";
var res = str.toLowerCase(); // abcdefg123456
var str = "ABCDefg123456";
var res = str.toUpperCase(); // ABCDEFG123456
```

## splice 删除

splice具有删除，插入，替换的功能

```js
// 1\查找指定元素的索引
function arrIndex(val, arr){
    for (var i = 0; i < arr.length; i++) {
        if (arr[i] == val) return i;
    }
    // 表示返回一个代数值，一般用在子函数结尾。
    return -1
}
// 2\删除索引元素
function removeCurEle(val, arr){
    var index = arrIndex(val, arr);  // 拿到下标 index
    if (index > -1) {
        arr.splice(index, 1);
    }
}
//测试一下
var testArr = ['a','b','c','d','e','f','g'];
removeCurEle('c', testArr);
console.log(testArr);   // ["a", "b", "d", "e", "f", "g"]
```
