# 1> 安装 

`npm install vue-i18n`

# 2> 布局，创建多语言的文件夹和js文件

![格局](http://120.79.222.255/icon-i18n.png)

- 最外层 index.js：

```js
import Vue from 'vue'
import VueI18n from 'vue-i18n'
import messages from './messages'
import { getStore } from '@/common/localUtil'

/**
 *  中英文切换
 */
const i18n = new VueI18n({
    locale: getStore('localeCut') || 'zh-CN',    // 语言标识
    messages
})

Vue.use(VueI18n)
export default i18n
```

- messages.js 暴露语言包

```js
import cn from "./cn/index"
import en from "./en/index"

export default {
    'en-US': en,
    'zh-CN': cn
}
```

# 3> main.js 全局暴露方法

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import i18n from './locale'

new Vue({
    router,
    store,
    i18n,
    render: h => h(App)
}).$mount('#app')

```

# 4> 导航栏切换

location.reload() 更新状态，setStore() 则缓存本地状态

```html
<Menu @on-select="onSwitchClick">
    <Submenu name="1">
        <template slot="title">
            <Icon type="md-globe" />
            <span>{{$t('homePage_001')}}</span>
        </template>
        <MenuItem name="zh-CN">简体中文</MenuItem>
        <MenuItem name="en-US">English</MenuItem>
    </Submenu>
</Menu>

<script>
    import { setStore } from '@/common/localUtil'

    methods: {
        /**
        *  中英切换
        *  - vuex 本地缓存 localeCut 切换语言状态
        */
        onSwitchClick(name) {
            setStore('localeCut', name)
            location.reload()
        }
    }
</script>
```