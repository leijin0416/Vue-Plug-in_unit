# Vue3 源码

数据是发布者，视图是订阅者，视图得知数据变化后，就会进行更新。

[【源码】bilibili VUE3.0 珠峰](https://www.bilibili.com/video/BV1nE411d7eW/?spm_id_from=333.788.videocard.22)

[【源码】bilibili VUE2.0 小马哥](https://www.bilibili.com/video/BV1qJ411W7YR)

[【源码】知乎](https://zhuanlan.zhihu.com/p/339237888)

[【掘金】 蚂蚁 Reactive篇](https://juejin.cn/post/6844903969894973448#heading-1) --|-- [【掘金】 蚂蚁 Effect篇](https://juejin.cn/post/6844903977599893517#heading-1) --|-- [【掘金】 ssh 从零实现](https://juejin.cn/post/6844904050014552072#heading-0)

![流程图](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/850be720158a4253a1fcb230191b3f68~tplv-k3u1fbpfcp-watermark.image)

```js
let dummy
let obj = { num: 0 }
const counter = reactive(obj)
effect(() => (dummy = counter.num))

console.log(dummy == 0)
counter.num = 7
console.log(dummy == 7)
```

[【掘金】Reflect.get()](https://juejin.cn/post/6844903969894973448#heading-7)

[【源码】原理 流程图 知乎](https://zhuanlan.zhihu.com/p/158743788)

## 执行流程

- 1、利用 reactive() 函数将 obj（目标对象） 变成响应式对象，返回一个 proxy 实例（代理对象），即 counter 代理对象。并利用了 Reflect() 方法把现有操作行为原模原样地反射到目标对象上。

- 2、effect(fn) 响应式副作用函数会将当前的 effect 设为 activeEffect，并运行 getter(() => counter.num) 取值。在取值过程中，读取 num 的值（counter.num）。

- 3、fn() 读取 num 的值，并赋值给 dummy。

- 4、利用 js 是单线程的，此时读取属性这个操作会触发 proxy 的属性 “读取拦截操作”，也就是get()函数，在拦截操作里会去 “收集依赖”，就是track()函数，而这个依赖是步骤 2 产生的 activeEffect。

- 5、counter.num = 7 这个操作会触发 proxy 的属性设置拦截操作，在这个拦截操作里，除了把新的值返回，还会触发刚才收集的依赖。在这个依赖里把 counter.num 赋值给 dummy(num 的值已经变为 7)。

![执行流程图](https://pic1.zhimg.com/80/v2-d4ca2f76d3e27ce71f94263afea1dd48_720w.jpg)

## WeackMap-targetMap 三维的数据结构

```js
const count = reactive({ num: 0 })
// effect默认没带lazy参数，先会执行effect
effect(() => {
  // effect用到对应响应式数据时，count.num get就已经收集好了该effect依赖
  // 同理，使用computed api时，
  console.log(count.num)
})
// computed依赖于count.num,也意味着该computed是count.num的依赖项
const computedNum = computed(() => 2 * count.num))
count.num = 7
/**
 * target:触发 track 的对象
 * 
 * target 就是被 proxy 的原对象，这里利用了 Reflect() 方法（把现有操作行为原模原样地反射到目标对象上）, 将 this 指向代理对象
 * key 是对象触发 get 行为以后的属性名称。比如 count.num 触发了 get 行为，num 就是 key
 * dep 是回调函数，也就是 effect 中调用了 count.num 的话，这个回调就是 dep，需要收集起来下次使用
 */

/**
 * 通过weackMap 创建一个 targetMap 结构（三维的数据结构），它用于存储依赖关系.
 * WeakMap<Target, Map<string | symbol, Set<ReactiveEffect>>>
 * Target 目标对象（被劫持的原始数据） -> key（KeyToDepMap，目标对象的属性名称） -> Dep（存放着监听函数effect的集合，回调函数） 映射关系.
 * 
 * Reflect()总结：对于代理数据的任何读写操作，都会通过Refelct反射到原始对象上.
 */
// 拆解 1、全局的 targetMap 是：
targetMap: {
  { num: 0 }: dep    
}

// 拆解 2、dep 则是：
dep: {
  num: Set { effection }
}

//////////////////// 集合 ////////////////////
{
  target: {
    key: [Dep, ... N]
  }
}
```

- targetMap 通过数据 target，可以获取到 depsMap，它用来存放这个数据对应的所有响应式依赖。

- depsMap 的每一项则是一个 Set 数据结构，而这个 Set 就存放着对应 key 的更新函数。

注意：在 get() 方法中，第一个入参 target 不能跟 Proxy构造函数 的第一个入参混淆。Proxy函数 的第一个入参 target 指的“原始数据”。而在 get()方法中，这个 target 其实是 “**被代理后的数据**”。也即是 Reflect.get(target, key, receiver) 中的 receiver。

![weackMap 收集依赖图](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f001dffdb5ef484da5d406c4639ce127~tplv-k3u1fbpfcp-watermark.image)

---

## effect() 和 reactive()

- reactive() 函数接收一个对象作为参数，并返回一个代理对象。

- effect() 函数用于定义副作用，它的参数就是副作用函数，这个函数可能会产生副作用，

- effect() 在 mountComponent、doWatch、reactive 中被调用，

- effect 的作用就是为了在响应式数据变化时执行它自己，`因此通常就是在回调里get响应式数据，后面修改这个数据时再触发回调执行`。

- 例如代码中的 `document.body.innerText = obj.text` **在副作用函数内的 “响应式数据” 会与 “副作用函数” 之间建立联系**，即所谓的 “**依赖收集**”，当响应式数据变化之后，会导致副作用函数重新执行。

```js
import { effect, reactive } from '@vue/reactivity'
// 使用 reactive() 函数定义响应式数据
const obj = reactive({ text: 'hello' })
// 使用 effect() 函数定义副作用函数
effect(() => {
  document.body.innerText = obj.text
})

// 一秒后修改响应式数据，这会触发副作用函数重新执行
setTimeout(() => {
  obj.text += ' world'
}, 1000)
```

在 Vue 中是使用了 hash 表来 “**实现避免重复代理**” 的，也就是使用了 WeakMap 来在第一次创建代理后缓存一个映射关系，下一次代理的时候如果之前已经代理过了就直接返回之前的代理。

## 所谓的 effect 是什么呢？

就是当前正在调用这个对象的函数。在我们的例子里面，**就是 return 回去的 render函数**，这个函数在执行的时候会调用 state.a 所以会进入 proxy 对于get的代理，这个时候 proxy 就调用了track，那么这时候的 **activeEffect 就是这个 render 方法**。这里的effect就是，当 state.a 改动的时候，我们需要重新执行该 render 方法来进行渲染。

在执行 render函数 的时候，因为我们调用了state.a 所以这个函数就相当于依赖 state.a，这在vue3里面被称为 effect。

实例：

```js
const Comp = {
  setup() {
    const state = reactive({
      a: 'jokcy'
    })
    
    return () => {
      return <input value={state.a} onChange={(e) => state.a = e.targent.value} />
    }
  }
}
```

![reactive-effect流程图](https://user-gold-cdn.xitu.io/2019/10/18/16ddc3b1f7fc9217?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

---

## track()

踪收集依赖(收集 effect)。track() 方法中最核心的一步就是 **deps.add(activeEffect)** ，其实的过程都是为了初始化一个数据结构，用于存储对应关系。

- target：要跟踪的目标对象，这里就是 obj

- 跟踪操作的类型：obj.foo 是读取对象的值，因此是 get

- key：要跟踪目标对象的 key，我们读取的是 foo，因此 key 是 foo

```js
const obj = { foo: 1 }
effect(() => {
  console.log(obj.foo)
  track(obj, TrackOpTypes.GET, 'foo')
})

obj.foo = 2
trigger(obj, TriggerOpTypes.SET, 'foo')
```

### 拆解

- obj 是一个普通的对象，注意它并非是响应式对象。

- 接着使用 effect() 函数定义了一个副作用函数，读取并打印 obj.foo 的值，这样 effect 与数据之间就会建立“联系”

- 由于 obj 是一个普通对象，因此它并没有收集依赖的能力，

- 为了收集到依赖，我们需要手动调用 track() 函数。

---

Vue3 对于响应式数据，不再像 Vue2 中那样递归对所有的子数据进行响应式定义了，而是**再获取到深层数据的时候再去利用 reactive 进一步定义响应式**，这对于大量数据的初始化场景来说收益会非常大。

初始化定义 reactive 的时候，只会对 obj 浅层定义响应式，而真正读取到 obj.foo 的时候，才会对 foo 这一层对象定义响应式，简化源码如下：

```js
const obj = reactive({
  foo: {
    bar: 1
  }
})

function get(target: object, key: string | symbol, receiver: object) {
  const res = Reflect.get(target, key, receiver)
  // 这段就是惰性定义
  return isObject(res)
    ? reactive(res)
    : res
}
```
