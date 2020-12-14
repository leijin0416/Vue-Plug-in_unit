# async/await

最简洁地写串行的异步操作。async 函数的实现原理，就是将Generator函数和自动执行器，包装在一个函数里。

## async/await的优缺点

优点|缺点
:-|:-:
相对于promise，async/await **处理 then 的调用链**，代码要清晰很多，几乎和同步代码一样。 | 滥用 await 可能会导致性能问题，因为 await 会阻塞代码。

```js
async function fn() {
  try {
    await new Promise((resolve, reject) => {
      setTimeout(() => {
        reject('err3')
      }, 1000)
    })
  } catch (err){
    alert(err)
  }
}
fn()

/**-------------------- END --------------------*/

// promise version:
let v1, v2;
promise1()
  .then(v => {v1=v; return promise2(v1)})
  .then(v => {v2=v; return promise3(v1,v2)})
  .then(v3 => { /*..v1,v2,v3...*/ })

/**-------------------- END --------------------*/

//  es6 async/await version:
async function asyn_run(){
  let v1 = await promise1()
  let v2 = await promise2(v1)
  let v3 = await promise3(v1, v2)
  /*... v1, v2, v3 ... */
}
async_run()  // actually non-blocking 同步代码

/**-------------------- END --------------------*/

function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms)
  })
}
async function asyncPrint(value, ms) {
  await timeout(ms)
  console.log(value)
}
asyncPrint('hello world', 50)  // 50毫秒以后，输出hello world
```

---

## Promise

[Promise核心原理 微信介绍](https://mp.weixin.qq.com/s/sNhCOQqGPZTTndGHJHZYQA) --||-- [Promise -async/await 简书介绍](https://www.jianshu.com/p/5f49ac47ffba)

Promise 是一个异步操作返回的对象，用来传递异步操作的消息。

它有三种状态: Pending 初始态； Fulfilled 成功态； Rejected 失败态。

首先要理解js语言的运行环境是单线程的，**也就是说一次只能完成一个任务，也就是一条流水线，如果有 “多个任务” 就 “必须排队”**，前面一个任务完成，再执行后面一个任务，以此类推。这与java的多线程环境截然不同。

可以改变程序执行顺序的操作就可以**看成为是 “异步操作”**。

**「概念：」** 利用 promise 可以**将异步操作以同步操作**的流程表达出来，**避免了层层嵌套的回调函数**。此外，promise对象提供统一的接口，使得控制异步操作更加容易。

**Promise是一个构造函数，通过它，我们可以创建一个Promise实例对象。Promise对象上有 `then() , catch() , finally()` 方法。**

Promise 是一个构造函数，使用时我们需要先使用 new 创建一个 Promise 实例。

但注意 promise 无法取消，一旦建立就会立即执行，**无法中途取消**。

```js
function loadImg(url) {
  let img = new Image();
  img.src = url;
  // promise
  return new Promise((resolve, reject) => {
    img.onload = () => {
      console.log(url);
      resolve();
    }
    img.onerror = (e) => {
      reject(e);
    }
  })
}


loadImg(url1).then(() => {
  return loadImg(url2);
}).then(() => {
  return loadImg(url3);
})
```

---

## 宏任务和微任务

**【宏任务】**：普通的script代码（同步代码），setTimeout，setInterval；

**【微任务】**：process.nextTick，promise.then()；

**【总体】** 来说就是：同步 > 微任务 > 宏任务；

JS在执行完同步代码之后，会首先去执行微任务队列中待执行的代码，然后再去执行宏任务中的代码

考察执行顺序：**按顺序执行代码, 当遇到await, 立即执行await后的函数, 然后将await之后的代码加入 “微任务”**

```js
// https://blog.csdn.net/weixin_43801564/article/details/89384255   -参考
async function async1() {
  console.log('async1 start');  // 2
  await async2();
  console.log('async1 end');    // 6
}
async function async2() {
  console.log('async2');        // 3
}
console.log('script start');    // 1   同步流程

setTimeout(function() {
  console.log('setTimeout');    // 8   宏任务
}, 0)

async1();

new Promise(function(resolve) {
  console.log('promise1');      // 4
  resolve();
}).then(function() {
  console.log('promise2');      // 7
});

console.log('script end');      // 5   同步流程

// script start
// async1 start
// async2
// promise1
// script end
// async1 end
// promise2
// setTimeout
```

假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。

我们仍然用 setTimeout 来模拟异步操作：

[红绿灯交替亮灯 案例知乎](https://zhuanlan.zhihu.com/p/26523836)

```js
/** https://www.jianshu.com/p/5f49ac47ffba   -简书参考
 *
 * 传入参数 n，表示这个函数执行的时间（毫秒）
 * 执行的结果是 n + 200，这个值将用于下一步骤
 */
function takeLongTime(n) {
  return new Promise(resolve => {
    setTimeout(() => resolve(n + 200), n);
  });
}

function step1(n) {
  console.log(`step1 with ${n}`);
  return takeLongTime(n);
}

function step2(n) {
  console.log(`step2 with ${n}`);
  return takeLongTime(n);
}

function step3(n) {
  console.log(`step3 with ${n}`);
  return takeLongTime(n);
}

// 用 Promise 方式来实现
function doIt() {
  console.time("doIt");
  const time1 = 300;
  step1(time1)
    .then(time2 => step2(time2))
    .then(time3 => step3(time3))
    .then(result => {
      console.log(`result is ${result}`);
      console.timeEnd("doIt");
    });
}
doIt();

// 用 async/await 来实现
async function doIt() {
  console.time("doIt");
  const time1 = 300;
  const time2 = await step1(time1);
  const time3 = await step2(time2);
  const result = await step3(time3);
  console.log(`result is ${result}`);
  console.timeEnd("doIt");
}

doIt();

// 两者结果是一样的
// step1 with 300
// step2 with 500
// step3 with 700
// result is 900
// doIt: 1507.251ms
```
