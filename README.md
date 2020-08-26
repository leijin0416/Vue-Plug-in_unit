# Vue 资料整理

[Vue执行流程分析](https://juejin.im/post/6860430490302677006) --|-- [Vue主动刷新页面及列表数据删除后的刷新方法](https://www.cnblogs.com/mica/tag/Vue/) --|-- [九种 Vue 组件间通讯](https://juejin.im/post/6861547167358648327)

[查API兼容](https://www.caniuse.com/#search=CSS%20Variables) --|-- [闭包，this指向问题](https://juejin.im/post/6864378349512065038) --|-- [TypeScript时遇到的一些有趣的代码](https://github.com/sl1673495?tab=repositories)

```js
// 模板文本
const db = 'http://' + host + ':' + port + '/' + database;
// 简化后
const db = `http://${host}:${port}/${database}`;

-------------------- END --------------------

// 解构赋值
const { store, form, loading, errors, entity } = this.props;
```

[对于JS简写](https://zhuanlan.zhihu.com/p/111457373)

## 递归拷贝

[JS高频手写实用方法](https://www.cnblogs.com/chenwenhao/p/11294541.html)

- **deepClone(obj[i])** -执行递归

- **hasOwnProperty()** -表示是否有自己的属性。这个方法会查找一个对象是否有某个属性，但是不会去查找它的原型链。

### 技巧

```js
// 由于 for in 总是要遍历整个原型链，因此如果一个对象的继承层次太深的话会影响性能。所以使用了 hasOwnProperty 来过滤
let obj = {a: 1, b: {x: 3}};
function deepClone(obj) {
    let copy = obj instanceof Array ? [] : {};
    for (let i in obj) {
        // 判断自有属性，过滤
        if (obj.hasOwnProperty(i)) {
            // 判断是否需要递归
            copy[i] = typeof obj[i] === 'object' ? deepClone(obj[i]) : obj[i];
        }
    }
    return copy
}

-------------------- END --------------------

// 深度克隆
let copy = {a: 1, b: {x: 3}};
function deepClone(obj) {
    let objClone = obj instanceof Array ? [] : {};
    // 如果他是空的话, 因为 null,object,array 也是'object';
    if (obj === null) {
        objClone = null;
    }
    if (obj && typeof(obj) === 'object') {
        for (key in obj) {
            // 判断对象里是否含有非继承属性 key，判断一个对象是否为空方法
            if (obj.hasOwnProperty(key)) {
                if (obj[key] && typeof(obj[key]) === 'object') {

                    objClone[key] = deepClone(obj[key]);
                    console.log('object');
                } else {

                    objClone[key] = obj[key];
                    console.log('Array');
                }
            }
        }
    }
    return objClone
}

let objCopy = this.deepClone(copy);
console.log(copy);

-------------------- END --------------------

// JSON.stringify(this.man) === '{}'   -将json对象转化为json字符串，再判断该字符串是否为"{}"
var man = {
    legs:2,
    hands:2,
    heads:1
}
man.hasOwnProperty("heads") // 返回 true，检测对象。判断 heads 的名称是不是 man对象 的一个属性或对象。

// ES6的Object.keys()方法
var data = {};
var arr = Object.keys(data);
alert(arr.length == 0);   // true
```

[Object.keys -返回索引](https://www.jianshu.com/p/da7ccf54e7f9) --|-- [Object.keys -兼容](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

#### Object 的 `hasOwnProperty()` 方法返回一个【布尔值】，检测对象是否包含特定的自身（非继承）属性

## 数组去重

[JavaScript数组去重的几种方法](https://blog.csdn.net/m0_37885651/article/details/79738780) --|-- [JavaScript 数组去重的几种方法-2](http://www.nowamagic.net/javascript/js_RemoveRepeatElement.php)

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

    return temp
}
var array = [1, 2, 2, 3, 5, 3, 6, 5];  // 只适用于数组项为字符串, 数字的一维数组
console.log(uniq(array));

-------------------- END --------------------

// 二维数组的排重 -递归大法
var arr = [["aa","bb","cc"],["aa","bb","cc"],["b","b","v"]];  
var hash = {};  
var result = [];  
for(var i = 0, len = arr.length; i < len; i++) {
    // 判断当前项是否遍历过，否存入hash以作对照
    if(!hash[arr[i]]) {  
        result.push(arr[i]);  
        hash[arr[i]] = true;  
    }  
}
console.log(result);

/**
 * 数组去重 -声明一个新数组
 * 遍历数组（可以用for循环，也可以用forEach）
 * check新数组中是否有这个元素，
 * 如果没有，就添加进去；如果有，就跳过。
 *
 */
function removeDuplicate(arr) {
    var resultArr = [];
    for(var i = 0; i < arr.length; i++) {
        var item = arr[i];
        if (resultArr.indexOf(item) < 0) {
            resultArr.push(item);
        }
    }
    return resultArr;
}
```

[hashtable -hash去重](https://www.cnblogs.com/sosoft/archive/2013/12/08/3463830.html)

## "Array.find()" -es6

- 该方法主要应用于查找第一个符合条件的数组元素。它的参数是一个回调函数。

- 在回调函数中可以写你要查找元素的条件，当条件成立为 true时，【**返回该元素**】。如果没有符合条件的元素，返回值为 undefined。

- 应用于【**数组去重**】。如：**find()、findIndex() 与 filter()**。findIndex() -IE兼容不行

[es6数组方法find()](https://www.jianshu.com/p/1c15be16a85a)

```js
const pets = [
    { type: 'Dog', name: 'Max'},
    { type: 'Cat', name: 'Karl'},
    { type: 'Dog', name: 'Tommy'},
]

function findDog(name) {
    for(let i = 0; i < pets.length; ++i) {
        if(pets[i].type === 'Dog' && pets[i].name === name) {
            return pets[i];
        }
    }
}

-------------------- END --------------------

// 简化
const pet = pets.find(pet => pet.type ==='Dog' && pet.name === 'Tommy');
console.log(pet); // { type: 'Dog', name: 'Tommy' }，只返回第一个满足条件的元素
// 又或者
const pet = pets.filter(pet => pet.type ==='Dog' && pet.name === 'Tommy');
console.log(pet); // [{ type: 'Dog', name: 'Tommy' }]，返回的是数组
```

[Array.filter()的妙用详解](https://www.jb51.net/article/99038.htm)

## "sort" 排序

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

-------------------- END --------------------

// 不传参数，将不会按照数值大小排序，【按照字符编码】的顺序进行排序；
var arr = ['General','Tom','Bob','John','Army'];
var resArr = arr.sort();
console.log(resArr);//输出 ["Army", "Bob", "General", "John", "Tom"]

-------------------- END --------------------

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

## "splice" 删除

splice具有删除，插入，替换的功能。 **`splice(index, howmany, item1,....., itemX)`**

- **index**     -必需。  整数，规定 **添加/删除** 项目的位置，使用负数可从数组结尾处规定位置。
- **howmany**   -必需。要删除的项目数量。如果设置为 0，则不会删除项目。
- item1, ..., itemX     -可选。向数组添加的新项目。

```js
// 1\ 查找指定元素的索引
function arrIndex(val, arr){
    for (var i = 0; i < arr.length; i++) {
        if (arr[i] == val) return i;
    }
    // 表示返回一个代数值，一般用在子函数结尾。
    return -1
}
// 2\ 删除索引元素
function removeCurEle(val, arr){
    var index = arrIndex(val, arr);  // 拿到下标 index
    if (index > -1) {
        arr.splice(index, 1);
    }
}
// 测试一下
var testArr = ['a','b','c','d','e','f','g'];
removeCurEle('c', testArr);
console.log(testArr);   // ["a", "b", "d", "e", "f", "g"]
```
