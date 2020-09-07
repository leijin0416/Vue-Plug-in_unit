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
                reject('err3');
            }, 1000);
        });
    } catch (err){
        alert(err);
    }
}
fn();

/**-------------------- END --------------------*/

// promise version:
let v1, v2;
promise1()
  .then(v => {v1=v; return promise2(v1)})
  .then(v => {v2=v; return promise3(v1,v2)})
  .then(v3 => { /*..v1,v2,v3...*/ });

/**-------------------- END --------------------*/

//  es6 async/await version:
async function asyn_run(){
  let v1 = await promise1();
  let v2 = await promise2(v1);
  let v3 = await promise3(v1, v2);
  /*... v1, v2, v3 ... */
}
async_run();  // actually non-blocking 同步代码

/**-------------------- END --------------------*/

function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}
async function asyncPrint(value, ms) {
  await timeout(ms);
  console.log(value);
}
asyncPrint('hello world', 50);  // 50毫秒以后，输出hello world
```
