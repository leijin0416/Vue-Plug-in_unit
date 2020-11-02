# 递归

**概念：** 在程序中函数 “直接” 或 “间接” 的自己调用自己。换个意思就是函数自己调用自己本身，或者在自己函数调用的下级函数中调用自己。
标签|内容
:-|:-:
[多叉树原理-用到递归来实现数据格式化](https://segmentfault.com/a/1190000015813977?utm_source=tag-newest) | ——
—— |
[【掘金】 理解递归](https://juejin.im/post/6844903699584647176) | [【详细】JS树结构操作:查找、遍历](https://wintc.top/article/20) +++ [递归权限思路](https://segmentfault.com/q/1010000021248164)

```js

let tree = [
  {
    id: '1',
    title: '节点1',
    children: [
      {
        id: '1-1',
        title: '节点1-1'
      },
      {
        id: '1-2',
        title: '节点1-2'
      }
    ]
  },
  {
    id: '2',
    title: '节点2',
    children: [
      {
        id: '2-1',
        title: '节点2-1',
        children: [
            {
                id: '2-1-1',
                title: '节点2-1-1'
            }
        ]
      }
    ]
  }
]

function treeForeach (tree, func) {
  if (!tree) return []
  tree.forEach(data => {
    func(data)
    data.children && treeForeach(data.children, func) // 遍历子树
  })
}

treeForeach(tree, node => { console.log(node.title) })
```

---

树状图-获取节点的所有叶子节点？ 递归代码如下：

```js
/**
 * 1|| 获取 节点的所有 叶子节点 个数
 * @param {Object} json Object对象
 */
function getLeafCountTree(json) {
    if(!json.children) {
        return 1;
    } else {
        let leafCount = 0;
        for (let i = 0; i < json.children.length; i++) {
            leafCount = leafCount + getLeafCountTree(json.children[i]);
        }
        return leafCount;
    }
};

/**
 * 2|| 获取 节点的所有 叶子节点 个数
 * @param {Object} array   Object对象      （需要遍历的对象）     在某个节点上寻找元素。
 * @param {String} uid     String字符串    （需要比较的字符串）   需要寻找的uid。
 * @param {Object} newData Object对象      （新的需要添加的对象） 找到的元素存储到这个数组中。
 */
function getLeafCountTree(array, uid, newData) {
    for (let index = 0; index < array.length; index++) {
        const item = array[index];
        if (item.uid == uid)                    // 1\ 判断递归结束条件
        {
            item.expand = false;
            // 判断children是否有值，防止重复点击/添加(push)
            if (item.children.length > 0) {
                item.children.length = 0;
            }
            if (newData.length > 0) {
                for (let j = 0; j < newData.length; j++) {
                    const element = newData[j];
                    item.expand = true;
                    item.children.push(element);
                }

            }
            return item;
        }
        else if (item.children.length > 0)         // 2\ 判断chlidren是否有数据
        {
            getLeafCountTree(item.children, uid, newData);  // 递归调用
        }
    }
}
```

- 2\ 查看这个子节点是否还有子节点，如果没有直接存储到数组中，如果有就继续递归。

**例子：**

[无限层级树形数据结构](https://blog.csdn.net/Mr_JavaScript/article/details/82817177)

```js
let source = [
    {id:1,parentId:0,name:"一级菜单A",rank:1},
    {id:2,parentId:0,name:"一级菜单B",rank:1},
    {id:3,parentId:0,name:"一级菜单C",rank:1},
    {id:4,parentId:1,name:"二级菜单A-A",rank:2},
    {id:5,parentId:1,name:"二级菜单A-B",rank:2},
    {id:6,parentId:2,name:"二级菜单B-A",rank:2},
    {id:7,parentId:4,name:"三级菜单A-A-A",rank:3},
    {id:8,parentId:7,name:"四级菜单A-A-A-A",rank:4},
    {id:9,parentId:8,name:"五级菜单A-A-A-A-A",rank:5},
    {id:10,parentId:9,name:"六级菜单A-A-A-A-A-A",rank:6},
    {id:11,parentId:10,name:"七级菜单A-A-A-A-A-A-A",rank:7},
    {id:12,parentId:11,name:"八级菜单A-A-A-A-A-A-A-A",rank:8},
    {id:13,parentId:12,name:"九级菜单A-A-A-A-A-A-A-A-A",rank:9},
    {id:14,parentId:13,name:"十级菜单A-A-A-A-A-A-A-A-A-A",rank:10},
];

/**
 * 思路：
 *      father.id == child.parentId -判断 父级id和子级parentId 是否相等，以此知道是否有下级。
 */
function setTreeData(source){
    let cloneData = JSON.parse(JSON.stringify(source))       // 对源数据深度克隆
    return cloneData.filter(father => {                      // 循环所有项，并添加children属性
        let branchArr = cloneData.filter(child => father.id == child.parentId);       // 返回每一项的子级数组
            branchArr.length > 0 ? father.children = branchArr : ''   //给父级添加一个children属性，并赋值
        return father.parentId==0;      //返回第一层
    });
}
console.log(setTreeData(source), '-END-')   // 树形数据

// data.splice(index, 1);   删除
data.forEach(function (item) {
    delete item.children;
});

```

**例子：**

[递归遍历方法 -简书](https://www.jianshu.com/p/e1c0faeca43c) -|- [数组递归遍历 -concat合并](https://segmentfault.com/q/1010000002681517)

```js
var data = [
 {
     name: "所有物品",
     children: [
         {
             name: "水果",
             children: [{name: "苹果", children: [{name: '青苹果'}, {name: '红苹果'}]}]
         },
         {
             name: '主食',
             children: [
                 {name: "米饭", children: [{name: '北方米饭'}, {name: '南方米饭'}]}
             ]
         },
         {
             name: '生活用品',
             children: [
                 {name: "电脑类", children: [{name: '联想电脑'}, {name: '苹果电脑'}]},
                 {name: "工具类", children: [{name: "锄头"}, {name: "锤子"}]},
                 {name: "生活用品", children: [{name: "洗发水"}, {name: "沐浴露"}]}
             ]
         }
  ]
}]

var recursiveFunction = function(){
    var str = '';
    const getStr = function(list) {
        list.forEach(function(row) {
            // 通过判断是否还有children，没有就代表是最后一级了，有就继续把 children 这个 list 传给函数继续遍历
            if (row.children) {
                getStr(row.children);
            } else {
                str += row.name + ";";
            }
        })
    }
    getStr(data);
    console.log(str); // 青苹果;红苹果;北方米饭;南方米饭;联想电脑;苹果电脑;锄头;锤子;洗发水;沐浴露;
}
recursiveFunction();

----------------------------------------

/**
 * 递归遍历实现 多个异步结果的依次输出
 *
 * @param {Object}   在setTimeout里面输出了之后，再开始下一个1s的输出。
 */
var recursiveTest = function(){
    console.time("递归时间");     // 开始
    const test = function (i) {
        setTimeout(function () {
            i = i + 1
            console.log('递归输出：' + i);
            if (i < 5) {
                test(i)
            } else {
                console.timeEnd("递归时间");   // 结束
            }
        }, 1000)
    }
    test(0)
}
recursiveTest()    // 每隔一秒输出1,2,3,4,5
```

---

## 深拷贝，使用递归方式

```js
function clone(o){
    var temp = {};
    for ( var key in o ) {

        if ( typeof o[key] === 'object' ) {

            temp[key] = clone(o[key]);   // 递归遍历

        } else {

            temp[key] = o[key];   // 值就返回

        }

    }
    return temp;
}


/**
 * 递归遍历实现 多个异步结果的依次输出
 *
 * @param {Object}   在setTimeout里面输出了之后，再开始下一个1s的输出。
 */
var obj = {
    a: {
        name: "maoguotao",
        age: 18,
        sex: 'M', },
    b: {
        name: "maoshuqin",
        age: 18,
        sex: 'M', },
    c: {
        name: "MGT360124",
        age: 18,
        sex: 'M',
        title: {
            job:"班长"
        }, },
};

//递归遍历对象
var arr = [];

function f(obj) {
    for( var i in obj ) {
        if ( typeof obj[i] === "object" ) {   // 如果子级还是一个Object对象，则继续递归遍历直到不是 -else
            f (obj[i]);
        } else {
            arr.push(obj[i]);
        }
    }
}

f(obj);
console.log(arr);//["maoguotao", 18, "M", "maoshuqin", 18, "M", "MGT360124", 18, "M", "班长"]
```
