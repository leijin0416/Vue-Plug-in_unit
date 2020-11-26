# ES6 去重

[去重1](https://blog.csdn.net/ZYS10000/article/details/109955135) ---||--- [去重2](http://blog.sina.com.cn/s/blog_be2200300102wvvq.html)

## 【1】ES5冒泡排序法，去重arr

```js
var arr = [{name: 'a',id: 1}, {name: 'a',id: 2}, {name: 'b',id: 3}, {name: 'c',id: 4},
 {name: 'c',id: 6}, {name: 'b',id: 6}, {name: 'd',id: 7}];

function deWeightThree() {
    for (var i = 0; i < arr.length - 1; i++) {
        for (var j = i + 1; j < arr.length; j++) {
            if (arr[i].name == arr[j].name) {
                arr.splice(j, 1);
                //因为数组长度减小1，所以直接 j++ 会漏掉一个元素，所以要 j--
                j--;
            }
        }
    }
    return arr;
}
var newArr = deWeight();
console.log('方法一：es5,newArr', newArr);
```

## 【2】ES6 Map()，去重arr

```js
const deWeightThree = (tree: any) => {
    let map = new Map();
    for (let item of arr3) {
        if (!map.has(item.name)) {
            map.set(item.name, item);
        }
    }
    return [...map.values()];
}
```

## ES6的set去重数组

- Set数据结构，它类似于数组，其成员的值都是唯一的。

- 利用 Array.from() 将Set结构转换成数组。

```js
function deWeightMap(array){
    return Array.from(new Set(array));
}
deWeightMap([1,1,2,3]) //[1,2,3]

// 利用拓展运算符（...）
let arr = [1,2,3,3];
let resultarr = [...new Set(arr)];
console.log(resultarr); //[1,2,3]
```

## 数组下标去重

```js
function deWeightMap(arr){
    var hash=[];
    for (var i = 0; i < arr.length; i++) {
        if(arr.indexOf(arr[i]) == arr.lastIndexOf(arr[i])){
            hash.push(arr[i]);
        }
    }
    return hash;
}
```
