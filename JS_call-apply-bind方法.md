# This 指向

[阮一峰的网络日志 -this 用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。

---

## 改变this指向的方法

- .call(),  call(thisScope, arg1, arg2, arg3...); **`第一个参数是函数执行上下文的对象，后面的需要把参数按顺序传递进去，立即调用`**;

- .apply(), apply(thisScope, [arg1, arg2, arg3...]); **`第一个参数是函数执行上下文的对象，后面的两个参数，且是把参数放在数组里，立即调用`**;

- .bind()  bind(thisScope, arg1, arg2, arg3...); **`改变this的指向，返回的是函数，便于稍后调用`**;

**注意：** 如果第一个参数不需要写，不需要改动函数上下文的对象的话，**记得写上 null值 来占位**。如上面例子中的 exp.apply(null, [' happy ',' every',' day'])写法。

[call和apply常见的应用写法](https://www.jianshu.com/p/d04a7b51ee7b)

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

### 相关问题使用的例子

让 array1 具备 Array 的 push 方法;

```js
// 【1】数组的扩充
var array1 = [12 , "foo" , {name "Joe"} , -2458];
var array2 = ["Doe" , 555 , 100];
Array.prototype.push.apply(array1, array2); // array1 值为 [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100]

---
// 【2】找出数组中的最大数
var a = [2, 4, 5, 7, 8, 10];
console.log(Math.max.apply(null, a)); //10
console.log(Math.max.call(null, 2, 4, 5, 7, 8, 10)); //10

---
// 当传入参数的个数是不确定时
function log(){
  console.log.apply(console, arguments);
};
log(1); // 1
log(1,2); // 1 2
```

### 【3】如何利用 call、apply 来做继承，或者 多继承

**构造函数法:** 它用构造函数模拟"类"(创建类（new）)，在其内部用 `this关键字` **指代新创建的 "实例对象"**。而类的属性和方法，还可以定义在 构造函数的 prototype对象之上。

Object.create()生成实例，不需要用到new。 IE9+

[阮一峰的网络日志 -构造函数法 +++++](http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html)

```js
// 继承
function Animal(name){      
  this.name = name;      
  this.showName = function(){      
    console.log(this.name);      
  }      
}      

function Cat(name){    
  Animal.call(this, name);    
}      

/**
 * Animal.call(this) 的意思就是使用this对象代替Animal对象，那么
 * Cat中不就有Animal的所有属性和方法了吗，Cat对象就能够直接调用Animal的方法以及属性了
 */
var cat = new Cat("TONY");     
cat.showName();          // TONY

---
// 对象继承2
var Parent = function() {
  this.name = "yjc";
  this.age = 22;
}
var child = {};
console.log(child);   // Object {} ,空对象

Parent.call(child);
console.log(child);   // Object {name: "yjc", age: 22}

---
// 多继承
function Class1(a,b) {
  this.showclass1 = function(a,b) {
    console.log(`class1: ${a},${b}`);
  }
}

function Class2(a,b) {
  this.showclass2 = function(a,b) {
    console.log(`class2: ${a},${b}`);
  }
}

function Class3(a,b) {
  Class1.call(this);
  Class2.call(this);
}

let arr10 = [2, 3];
let demo = new Class3();     // 生成实例的时候，使用 "new关键字"

demo.showclass1.call(this,1);         // class1: 1,undefined
demo.showclass1.call(this,1,2);       // class1: 1,1
demo.showclass2.apply(this,arr10);    // class2: 2,2
```

[知乎见解 +++++](https://www.zhihu.com/question/20289071/answer/93261557)

### createNew() 继承，只要在前者的 createNew()方法中，调用后者的 createNew()方法即可

**其定义在对象上的方法和属性，都是私有的。**
