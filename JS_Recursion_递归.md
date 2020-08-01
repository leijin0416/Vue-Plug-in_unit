# 递归

**概念：** 在程序中函数 “直接” 或 “间接” 调用自己。换个意思就是函数自己调用自己本身，或者在自己函数调用的下级函数中调用自己。

[多叉树原理-用到递归来实现数据格式化](https://segmentfault.com/a/1190000015813977?utm_source=tag-newest)

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

例子： [无限层级树形数据结构](https://blog.csdn.net/Mr_JavaScript/article/details/82817177)

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
    let cloneData = JSON.parse(JSON.stringify(source))      // 对源数据深度克隆
    return cloneData.filter(father => {                      // 循环所有项，并添加children属性
        let branchArr = cloneData.filter(child => father.id == child.parentId);       // 返回每一项的子级数组
            branchArr.length > 0 ? father.children = branchArr : ''   //给父级添加一个children属性，并赋值
        return father.parentId==0;      //返回第一层
    });
}
console.log(setTreeData(source), '-END-')   // 树形数据

// data.splice(index, 1);
data.forEach(function (item) {
    delete item.children;
});

```

---

## 深拷贝，使用递归方式

```js
function clone(o){

    var temp = {};
    for(var key in o){

        if (typeof o[key] == 'object') {

            temp[key] = clone(o[key]);

        } else {

            temp[key] = o[key];

        }

    }

    return temp;
}
```
