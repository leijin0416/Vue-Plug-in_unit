# 节流/防抖

标签|内容
:-|:-:
[节流/防抖 -代码拆解分析](https://www.cnblogs.com/denghaopositive/p/12546645.html) —— [效果动图](https://www.cnblogs.com/smallpen/p/10289050.html) | 防抖和节流 都是为了 解决频繁触发某个事件 的情况造成的性能消耗
[【微信】 介绍解析例子](https://mp.weixin.qq.com/s/_T1Z8PN-vMQRfSu3bzMU1A) | [【博客园】 12-23 this指向解析例子](https://www.cnblogs.com/cc-freiheit/p/10827372.html) --——-- [【博客园】 12-23 解析例子](https://www.cnblogs.com/Antwan-Dmy/p/10714445.html)
12-24 | [【博客园】 this指向 评论总结](https://juejin.cn/post/6844903466427482120#heading-1)
 | ——
【 防抖 】 | 重复执行操作，只执行最后一次。例如：在进行**搜索（百度搜索）**的时候，当用户停止输入后调用方法，节约请求资源
【 节流 】 | 像玩射击游戏, 并不是你鼠标点的越快，射击的越快。节流和这一样，规定的时间内只执行一次。

## 关于

**函数防抖：** 将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

**函数节流：** 使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。比如 “懒加载” 时要监听计算滚动条的位置，但不必每次滑动都触发，可以降低计算的频率，而不必去浪费资源；

**区别：** 函数节流 不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而 函数防抖 只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用 节流技术 来实现。

- **`防抖：`** 是将多次执行变为最后一次执行，如 “`百度搜索`” 等。

- **`节流：`** 是将多次执行变为每隔一段时间执行，如 “滚动到底部加载更多” 或者 “按钮不断点击”，单位时间内只触发一次。

---

[【segmentfault】 介绍 全](https://segmentfault.com/a/1190000018428170)

## js 函数节流 event 对象如何传递问题？

[【segmentfault】 问题 概括](https://segmentfault.com/q/1010000011036337) --/-- [【知乎】 例子 方法](https://zhuanlan.zhihu.com/p/51608574)

1、传给 throttle 函数的 event 参数为当前全局的事件对象（可能为 undefined，也可能是其他事件，取决于你调用的环境），所以造成传给 keydownHandler 的 event 也始终是全局的事件对象。

2、当你触发键盘事件的时候，如果之前传递的全局事件对象为 undefined，则 keydownHandler 内部始终通过 window.event 来获取事件对象，而由于 setTimeout 的异步效果，当函数被调用的时候，事件对象已经被回收，所以获取 keyCode 属性会显示异常。

3、如果之前传递的全局事件对象为确实存在，则在 keydownHandler 内打印 event 得到的就是之前传递过来的全局事件对象。

### 解决

`fnName.call(null, event)` || `fnName.apply(null, event)`

[【张生荣】 博客](https://www.zhangshengrong.com/p/OgN5nvQOXn/) --/-- [【微信】 防抖节流 案例](https://mp.weixin.qq.com/s/RK1xYN4yb7l2sCv6sIOvcQ) --/-- [【掘金】 案例](https://juejin.im/post/6844904080540729357)

### 防抖-非立即函数和立即执行函数

```js
// 不管 “移入移出” 多少次， 不限点击次数（delay时间会叠加），每2秒内只会执行一次 myEvent()方法 
<p id="mydiv" style="width: 100px; height: 100px;border: 1px solid #ddd;">尝试移入移出 div。</p>

// 1、非立即执行防抖
function debounce1(fn, delay) {
  // 2、创建一个标记用来存放定时器的返回值
  let timeout;
  // 3、dom事件操作中，会给回调函数传递一个event参数
  return function () {
    let _that = this;       // 修复指向
    // arguments中存着event对象，一个为能实现重载函数功能的工具
    let _args = arguments;  

    // 4、每次当用户点击/输入的时候，把前一个定时器清除
    if (timeout) clearTimeout(timeout);
    // 5、然后创建一个新的 setTimeout，
    timeout = setTimeout(() => {
      // 6、apply让fn中的this指向了myEvent，dom对象，保证指向一致
      fn.apply(_that, _args);
    }, delay);
  }
}

// 立即执行防抖
function debounce2(fn, delay) {
  let timeout;
  return function() {
    let context = this;
    let args = arguments;
    if (timeout) clearTimeout(timeout);
    let callNow = !timeout;
      // 已经执行过，不再执行
      timeout = setTimeout(function() {
        timeout = null;
      }, delay);
      if (callNow) fn.apply(context, args);
  };
}

var mydiv = document.getElementById('mydiv');
let count = 0;
// 事件
function myEvent() {
  console.log(`执行时间：${new Date()}`);
  console.log(this);  // window 对象 document
  mydiv.innerText = count++;
}

// 这里会等2秒后执行函数
mydiv.addEventListener('mouseover', debounce1(myEvent, 2000));
// 这里会执行函数，连续点击按每2秒执行一次
mydiv.addEventListener('mouseover', debounce2(myEvent, 2000));

```

## 案例

```js
// 事件绑定，键盘事件  每3秒执行一次 keydownHandler()方法，直到停止点击  适合输入框
window.addEventListener('keydown', throttle(keydownHandler, 3000));

// 时间戳版本实现防抖 + 节流 操作
function throttle(fun, delay) {
  let last, deferTimer
  return function () {
    let _that = this;       // 指向
    let _args = arguments;  // arguments中存着e，为能实现重载函数功能的工具

    // 当前时间戳
    let now = +new Date();
    // 比较时间戳
    if (last && now < last + delay) {
      clearTimeout(deferTimer);
      deferTimer = setTimeout(function () {
        // 将旧时间更新
        last = now;
        fun.apply(_that, _args); // 修正this指向问题
      }, delay)
    } else {
      last = now;
      fun.apply(_that, _args);  // 返回响应
    }
  }
}

// 获取键盘事件
function keydownHandler(event) {
  let e = event || window.event,
    keyCode = e.keyCode || e.which;
  console.log(event);
}
```

1.throttle 函数的执行环境具有全局性，内部 this 通常是指向 window 的，然后返回一个匿名函数。
2.返回的匿名函数绑定了事件，this 指向监听的元素（document）。
3.fun 其实与上面返回匿名函数形成了闭包，且 fun 也其实是一个匿名函数，匿名函数的执行具有全局性，fun 内部this应该指向window。
4.这里用 apply 修正 this 指向，使 fun 内部的t his 重新指向 document。

## 问题总结

**throttle函数 在` 绑定 keydown事件 `的时候` 被调用 `，这个事件绑定发生在` window.onload `的事件处理程序中，此时全局 event对象 就是 type 为 load的事件对象，所以传入 throttle函数 的event 就是 type 为 load的事件对象。**

---

## 实例

```js
/*
 *  description: 在vue中使用的节流函数
 *  param fnName {String}  函数名
 *  param time {Number}    延迟时间
 *  return: 处理后的执行函数
 */
function vueThrottle(fn, time) {
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
// 立即执行
function throttle(func, wait) {
  let previous = 0;
  return function() {
    let now = Date.now();
    let context = this;
    let args = arguments;
    if (now - previous > wait) {
      func.apply(context, args);
      previous = now;
    }
  }
}

/*
 *  description: 在vue中使用的防抖函数
 *  param fnName {String}  函数名
 *  param time {Number}    延迟时间
 *  return: 处理后的执行函数
 */
function vueDebounce(fnName, time) {
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


/*
 *  description: 在vue中使用的防抖函数
 *  param fnName {String}  函数名
 *  param time {Number}    延迟时间
 *  return: 处理后的执行函数
 */
function VueDebounce(fnName, debounceTimes) {
  let timeout = null;
  let time = debounceTimes;   // 初次随机
  console.log(`初次随机时间: ${time}`);
  
  // 闭包
  return function(args) {
    let _that = this;
    let _args = arguments;

    if (timeout) clearTimeout(timeout);

    timeout = setTimeout(() => {
      fnName.apply(_that, _args);
    }, time);

    // 再次随机
    time = DebounceTimeArr();
    console.log(`再次随机时间: ${time}`);
  }
}

// 随机时间
function DebounceTimeArr() {
  let timeArr = parseInt(Math.random() * 60 + 1) * 1000;
  return timeArr
}

previewTimeDebounce: VueDebounce(onOtcBuysSubmitClick, otcTimeArr()),
```
