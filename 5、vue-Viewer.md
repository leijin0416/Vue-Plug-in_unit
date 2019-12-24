# 1> 安装 

`npm install --s v-viewer`

# 2> main.js 全局引入

```js
import Viewer from 'v-viewer'

/**
 *  v-viewer
 *  引入图片查看器
 */
Vue.use(Viewer)
Viewer.setDefaults({
    defaultOptions: {
        zIndex: 9999
    }
})
```

# 3> 组件使用

```html
<viewer :images="photoImg" v-viewer="{toolbar: false}">
    <div class="ivu-viewer">
        <img class="img"
            :src="image"
            title="点击查看大图"
            alt="chats.png">
    </div>
</viewer>

<script>
    data() {
        return {
            photoImg: [],
            image1: ''
        }
    },

</script>
```