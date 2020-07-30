# 递归

概念：在程序中函数 “直接” 或 “间接” 调用自己。

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
 * @param {Object} array   Object对象      （需要遍历的对象）
 * @param {String} uid     String字符串    （需要比较的字符串）
 * @param {Object} newData Object对象      （新的需要添加的对象）
 */
function getLeafCountTree(array, uid, newData) {
    for (let index = 0; index < array.length; index++) {
        const item = array[index];
        if (item.uid == uid)// 1\ 判断递归结束条件
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
        else if (item.children.length > 0) // 2\ 判断chlidren是否有数据
        {
            getLeafCountTree(item.children, uid, newData);  // 递归调用
        }
    }
}
```
