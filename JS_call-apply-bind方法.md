# This 指向

[阮一峰的网络日志 -this 用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。

---

## 改变this指向的方法

- .call(),  call(thisScope, arg1, arg2, arg3...); **`需要把参数按顺序传递进去，立即调用`**;

- .apply(), apply(thisScope, [arg1, arg2, arg3...]); **`两个参数，且是把参数放在数组里，立即调用`**;

- .bind()  bind(thisScope, arg1, arg2, arg3...); **`改变this的指向，返回的是函数，便于稍后调用`**;

```js
let obj = {
  name: 'ZhangSan',
  say: function() {
    console.log('hello' + this.name);
  }
}

function foo(item) {
  console.log(this.name + '-SAYHELLO-' + item);
}
foo.call(obj, 'AA'); // ZhangSan-SAYHELLO-AA   改变了指向，并立即调用

---

function fruits() {}
fruits.prototype = {
  color: "red",
  say: function() {
    console.log("My color is " + this.color);
  }
}
var app = new fruits;
app.say();     // My color is red

/*
 * 当一个 object 没有某个方法（banana没有say方法），但是其他的有（app有say方法）时，
 * 即可以借助 call 或 apply 用其它对象的方法来操作。
 */
banana = {
  color: "yellow"
}
app.say.call(banana); // My color is yellow  操作
app.say.apply(banana); // My color is yellow  操作
```

### 数组之间追加

让 array1 具备 Array的push 方法;

```js
var array1 = [12 , "foo" , {name "Joe"} , -2458];
var array2 = ["Doe" , 555 , 100];

// array1 值为 [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100]
Array.prototype.push.apply(array1, array2);

---

// 当传入参数的个数是不确定时
function log(){
  console.log.apply(console, arguments);
};
log(1); // 1
log(1,2); // 1 2
```

https://www.jianshu.com/p/d04a7b51ee7b
