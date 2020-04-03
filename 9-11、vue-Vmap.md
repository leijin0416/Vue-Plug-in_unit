# vue-amap 玩法

1、官网申请密钥: https://lbs.amap.com 申请密钥

应用管理 > 我的应用

文档：https://elemefe.github.io/vue-amap/#/zh-cn/coverings/marker

### 2、下载安装：

```js
npm install vue-amap --save
```

### 3、在 main.js 文件中引入并配置 vue-amap

```js
import VueAMap from 'vue-amap'

Vue.use(VueAMap)
VueAMap.initAMapApiLoader({
    key:"高德平台申请的key",
    plugin:['AMap.Autocomplete', 'AMap.PlaceSearch', 'AMap.Scale', 'AMap.OverView', 'AMap.ToolBar', 'AMap.MapType', 'AMap.PolyEditor', 'AMap.CircleEditor'],   //插件
    v:"1.4.4"  //版本号，默认高德sdk版本为1.4.4，可自行修改
})
```

### 3、创建组件：vueAmap

提示框区别：

（1）、componentMarker 地址横向提示框：

```vue
<template>
<!-- 横向模块提示框 -->
<el-amap-marker vid="component-marker" :position="componentMarker.position" :content-render="componentMarker.contentRender" ></el-amap-marker>
</template>

<script>
const exampleComponents = {
    props: ['text'],
    template: `<div>深圳市宝安国际机场</div>`
}
data() {
    return {
        componentMarker: { //横向模块
            position: [113.814829,22.633092],
            contentRender: (h, instance) => h(exampleComponents,{style: {backgroundColor: '#fff'}, props: {text: 'father is here'}}, ['xxxxxxx'])
        }
    }
}
</script>
```

（2）、window 窗口按钮提示框：

```vue
<template>
<!-- 窗口模块提示框 -->
<el-amap-info-window :position="window.position" :visible="window.visible" :content="window.content"></el-amap-info-window>
</template>

<script>
data() {
    return {
        window: {
            visible: true,
            position:  [113.814829,22.633092],
            content: '深圳市宝安国际机场',
            events: {
                close() {
                    this.window.visible = false;
                }
            }
        },
    }
}
</script>
```
### 模板：

| 属性 | 描述 |
| ------ | ------ |
| zooms | 地图显示的缩放级别范围。 |
| center | 坐标位置。 |
| plugin | 插件工具。文档有介绍， 如：`pName: 'Scale',` |
| markers | 点击地图点图标操作 |

```vue
<template>
<div class="amap-demo">
    <div class="amap-page-container" style="">
        <el-amap 
                vid="amapDemo" 
                :plugin="plugin"
                :zoom="zoom" 
                :center="center" 
                class="amap-demo">
                <!-- 横向模块提示框 -->
                <!-- <el-amap-marker vid="component-marker" :position="componentMarker.position" :content-render="componentMarker.contentRender" ></el-amap-marker> -->
                <el-amap-marker v-for="(marker, index) in markers" :position="marker.position" :events="marker.events" :visible="marker.visible" :draggable="marker.draggable" :vid="index"></el-amap-marker> 
                <!-- 按钮模块窗口 -->
                <el-amap-info-window :position="window.position" :visible="window.visible" :content="window.content"></el-amap-info-window>
        </el-amap>
    </div>
</div>
</template>

<script>
    const exampleComponents = {
      props: ['text'],
      template: `<div>深圳市宝安国际机场</div>`
    }
    module.exports = {
        name: 'amap-page',
        data() {
            return {
                count: 1,
                zoom: 13,
                center: [113.814829,22.633092],
                plugin: [{//插件
                    pName: 'Scale',  //比例尺
                    events: {
                        init(instance) {
                            console.log(instance);
                        }
                    }
                },{
                    pName: 'ToolBar',  //工具条
                    events: {
                        init(instance) {
                            console.log(instance);
                        }
                    }
                } ],
                markers: [
                    {
                        position: [113.814829,22.633092],
                        events: {
                            click: () => {
                                this.window.visible = false;
                                this.$nextTick(() => {
                                    // console.log(this.window);
                                    this.window.visible = true;
                                });
                            },
                            dragend: (e) => {
                                console.log('---event---: dragend')
                                // this.markers[0].position = [e.lnglat.lng, e.lnglat.lat];
                            }
                        },
                        visible: true,
                        draggable: false
                    }
                ],
                window: {  //1、窗口模块
                    visible: true,
                    position:  [113.814829,22.633092],
                    content: '深圳市宝安国际机场',
                    events: {
                        close() {
                            this.window.visible = false;
                        }
                    }
                },
                componentMarker: { //2、横向模块
                    position: [113.814829,22.633092],
                    contentRender: (h, instance) => h(exampleComponents,{style: {backgroundColor: '#fff'}, props: {text: 'father is here'}}, ['xxxxxxx'])
                }
            };
        },
        methods: {

        }
    };
</script>

<style lang="scss" scoped>
.amap-demo {
    width: 100%;
    height: 400px;
    font-size: 12px;
}
</style>
```