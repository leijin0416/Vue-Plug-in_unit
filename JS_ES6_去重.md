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
var newArr = deWeightThree();
console.log('方法一：es5,newArr', newArr);
```

## 【2】ES6 Map()，去重arr

Map 是一组键值对的结构，具有极快的查找速度。

```js
const deWeightThree = (tree: any) => {
    let map = new Map(); // 空Map
    for (let item of arr3) {
        // 是否存在key 'item.name':  返回 true，这里用的 false
        if (!map.has(item.name)) {
            map.set(item.name, item);  // 添加新的 key-value
        }
    }
    return [...map.values()]; // 扩展运算符，获取Map集合的value集合
}
```

## ES6的set去重数组

- Set数据结构，它类似于数组，其成员的值都是唯一的。

- 利用 Array.from() 将Set结构转换成数组。

- **可以去掉NaN**，不能去重复杂数组类型，运行速度快，耗时最少的方法之一。

```js
function deWeightMap(array) {
    return Array.from(new Set(array));
}
deWeightMap([1,1,2,3]) //[1,2,3]

// 利用拓展运算符（...）
let arr = [1,2,3,3];
let resultarr = [...new Set(arr)];
console.log(resultarr); //[1,2,3]
```

## filter() + hasOwnProperty() + JSON.stringify() -可以去掉NaN和复杂数据类型

```js
function deWeightMap(array) {
    let obj = {};
    return array.filter(function (item, index, arr) {
        return obj.hasOwnProperty(typeof item + JSON.stringify(item)) ? false : (obj[typeof item + JSON.stringify(item)] = true);
    });
}
```

## indexOf() 数组下标去重

**方法一：**

indexOf 还有一个兄弟方法 lastIndexOf，这两个方法返回的位置不一样则说明了这个值有重复啦

```js
// 不用新建数组副本
function clearArr(arr) {
    arr.forEach(function(item, i) {
        if (arr.indexOf(item) !== lastIndexOf(item)) {
            arr.splice(index, 1);
        }
    })
    return arr;
}

---

function deWeightMap(arr) {
    var hash = [];
    for (var i = 0; i < arr.length; i++) {
        if(arr.indexOf(arr[i]) === arr.lastIndexOf(arr[i])) {
            hash.push(arr[i]);
        }
    }
    return hash;
}
```

**方法二：**

- filter() 就是一个数组过滤器，参数接收一个函数，数组的每一项经过函数过滤，返回一个符合过滤条件的新数组。

- indexOf() 在JS中有着重要的作用，**可以判断一个元素是否在数组中存在**，或者判断一个字符是否在字符串中存在，**如果存在返回该元素或字符第一次出现的位置的索引**，不存在返回-1。用 indexOf 来判断 NaN 始终会返回 -1。

```js
let arr1 = [1, 2, 3, 4, 5, 6, 7, 8, 2, 3, 4, 5, 6, 7]

let newArr = arr1.filter(function(item, i, self) {
    let a = self.indexOf(item)
    console.log(`item----${item},self.indexOf(item)---${a},i----${i}`)
    return self.indexOf(item) === i;
});

console.log(newArr) // [1, 2, 3, 4, 5, 6, 7, 8]
```
