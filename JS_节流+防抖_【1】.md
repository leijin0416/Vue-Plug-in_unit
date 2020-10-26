# 节流/防抖

标签|内容
:-|:-:
[节流/防抖 -代码拆解分析](https://www.cnblogs.com/denghaopositive/p/12546645.html) —— [效果动图](https://www.cnblogs.com/smallpen/p/10289050.html) | 防抖和节流 都是为了 解决频繁触发某个事件 的情况造成的性能消耗
防抖： | 重复执行操作，只执行最后一次。例如：在进行搜索的时候，当用户停止输入后调用方法，节约请求资源
节流： | 像玩射击游戏, 并不是你鼠标点的越快，射击的越快。节流和这一样，规定的时间内只执行一次

## 总结

**函数防抖：** 将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

**函数节流：** 使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

**区别：** 函数节流 不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而 函数防抖 只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用 节流技术 来实现。

```js
/*
 *  description: 在vue中使用的节流函数
 *  param fnName {String}  函数名
 *  param time {Number}    延迟时间
 *  return: 处理后的执行函数
 */
function throttle(fn, time) {
    let _arguments = arguments;
    let canRun = true;

    return function () {
        if (!canRun) return
        canRun = false
        setTimeout(() => {
            fn.call(this, _arguments)
            canRun = true
        }, time);
    }
}

/*
 *  description: 在vue中使用的防抖函数
 *  param fnName {String}  函数名
 *  param time {Number}    延迟时间
 *  return: 处理后的执行函数
 */
function VueDebounce(fnName, time) {
    let debounceTime = time;
    let timeout = null;

    return function() {
        if (timeout) {
            clearTimeout(timeout);
        }
        timeout = setTimeout(() => {
            this[fnName]();
        }, debounceTime);
        // debounceTime = 1;  // 再次赋值时间
    };
}
```
