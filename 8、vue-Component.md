# require.context

一个 webpack 的api，通过执行 `require.context` 函数获取一个特定的上下文，**主要用来实现自动化导入模块**。

官网介绍：https://www.html.cn/doc/webpack2/guides/dependency-management/

### require.context函数接受三个参数

- directory {String} -读取文件的路径

- useSubdirectories {Boolean} -是否遍历文件的子目录

- regExp {RegExp} -匹配文件的正则

> 语法: require.context(directory, useSubdirectories = false, regExp = /^.//);

参考文章：https://www.jianshu.com/p/c894ea00dfec

---

# 1、优化全局组件注册

创建全局.js文件管理全局组件

### 1、创建 globalComponent.js 文件

```js
import Vue from 'vue' // 引入vue

// 处理首字母大写 abc => Abc
function changeStr(str){
    return str.charAt(0).toUpperCase() + str.slice(1)
}

/* require.context(arg1,arg2,arg3)
   -arg1 - 读取文件的路径
   -arg2 - 是否遍历文件的子目录
   -arg3 - 匹配文件的正则
    关于这个Api的用法，建议小伙伴们去查阅一下，用途也比较广泛
*/
const requireComponent = require.context('.', false, /\.vue$/)
console.log('requireComponent.keys():',requireComponent.keys())  // 打印

requireComponent.keys().forEach(fileName => {
    const config = requireComponent(fileName)
    console.log('config:', config)  // 打印

    const componentName = changeStr(
        fileName.replace(/^\.\//, '').replace(/\.\w+$/, '')   // ./child1.vue => child1
    )
    
    Vue.component(componentName, config.default || config) // 动态注册该目录下的所有.vue文件
})
```

### 2、将 globalComponent.js 引入 main.js 文件

```js
import global from './components/globalComponent'
```

### 3、最后，使用这类组件不再需要引入和注册，直接标签使用即可
```vue
<template>
    <div>
        <h1>I am HelloWorld</h1>
        <Child1></Child1>
    </div>
</template>
```

参考文章：https://juejin.im/post/5e475829f265da57444ab10f

---

# 2、require.context() 创建路由分区

```js
// 总路由管理文件 
    - index.js

// 分区路由
    - index.routes.js
    - login.routes.js
```

### 1、分区路由文件

```js
/**
 *  路由懒加载
 */
const getComponentMin = (name, component) => () =>
    import(`@/views/${name}/${component}.vue`)

export default [
    {
        path:'/login',
        name:'Login',
        meta: { title: '绿色星球' },
        component: getComponentMin('logins', 'login'),// 懒加载式引入，当跳转到时才进行引入chunk
        children: [...]
    }
]
```

### 2、总路由管理文件 index.js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routerList = []  // 路由数组 - 存放所有路由
function importAll(routerArr){
    // 该函数用于将所有分区路由中的路由添加到路由数组
    routerArr.keys().forEach( key => {
        console.log(key)
        routerList.push(routerArr(key).default)
    })
}
/**
 * require.context()
 */
importAll(require.context('.',true,/\.routes\.js/))

const routes = [
    ...routerList
]

const router = new VueRouter({
    routes
})

export default router
```

webpack是根据你文件夹中文件的位置排序的，可以给每个文件夹最上层的路由添加一个sort属性用于排序