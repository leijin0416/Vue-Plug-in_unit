# 1> 安装 

`npm install --s echarts`

[官方实例地址](https://www.echartsjs.com/examples/zh/index.html#chart-type-pie)

# 2> 组件局部引入

```js
import echarts from 'echarts'

```

# 3> 组件使用方法

- **1、新建JS，引用echarts中具体的实例;**

```js
// 饼状图
export const option = {
    // 标题
    title: {
        text: '绿色星球平台统计分析图',
        x: 'center',
        textStyle: {
            fontWeight: 'normal',
            fontSize: 16,
            color: '#333'
        }
    },    
    // 设置图表边距
    grid:{
        left: 0,
        right: 0,
        top: 15,
        bottom: 0,
        containLabel: true
    },
    // 提示
    tooltip: {
        trigger: 'item',
        formatter: '{a} <br/>{b} : {c} ({d}%)'
    },
    legend: {
        left: 'center',
        top: 'bottom',
        data: ['平台用户总人数', '本月新增用户', '今日新增用户', '养分出售总额', '养分回收爱心值', '正使用养分总额']
    },
    toolbox: {
        show: true,
        feature: {
            mark: { show: true },
            dataView: { show: true, readOnly: false },
            magicType: {
                show: true,
                type: ['pie', 'funnel']
            },
            restore: { show: true },
            saveAsImage: { show: true }
        }
    },
    /*
     *  动态数据
     *  center -位置偏移量
     */
    series: [
        {
            name: '用户统计',
            type: 'pie',
            radius: [30, 110],
            center: ['25%', '50%'],
            roseType: 'area',
            data: []
        },
        {
            name: '养分统计',
            type: 'pie',
            radius: [30, 110],
            center: ['80%', '50%'],
            roseType: 'area',
            data: []
        }
    ]
}
```

- **2、在组件模板中引入JS，并将其挂载到页面上;**

`问题： 1、遇到如何解决 echart 宽高自适应的问题？`

解决方案：在 created() `【数据data已经初始化完成，方法也已经可以调用，但是DOM未渲染。】` 在挂载DOM前拿到父节点（echartBox）的宽度，因为还没挂载DOM，此时拿到宽度（offsetWidth）浏览器会报错，所以利用 setTimeout() 延迟去拿宽度，这样浏览器不会报错

```html
<template>
    <Row>
        <Col span="24">
            <div class="chart-container" id="echartBox">
                <!--创建一个echarts的容器，给定高宽-->
                <div id="gamechart" class="ivu-echart" />
            </div>
        </Col>
    </Row>
</template>

<script>
// 安装echarts后，直接引入
import echarts from 'echarts'
import { option } from './cakechart'

export default {
    data() {
        return {
            chart: {},
            option: option,
            echartWidth: ''  // 默认宽度
        }
    },
    created() {
        this.fetchData()
        setTimeout(() => {
            let chartBox = document.getElementById('echartBox')
            this.echartWidth = chartBox.offsetWidth
            console.log(chartBox.offsetWidth);  // 1920
        }, 300)
    },
    // 挂载图表函数
    mounted() {
        let that = this
        that.initChart()
    },
    methods: {
        fetchData() {
            this.chart = {
                lastday: [
                    { value: 10, name: '平台用户总人数' },
                    { value: 5, name: '本月新增用户' },
                    { value: 15, name: '今日新增用户' }
                ],
                lastdays: [
                    { value: 10, name: '养分出售总额' },
                    { value: 5, name: '养分回收爱心值' },
                    { value: 15, name: '正使用养分总额' }
                ]
            }
            this.option.series[0].data = this.chart.lastday
            this.option.series[1].data = this.chart.lastdays
        },
        initChart() {
            let that = this
            // 需要缓存 width
            let myChartsize = document.getElementById('gamechart')
            let width  = that.echartWidth
            if (width == 0) {
                myChartsize.style.width = '1000px'
            } else {
                myChartsize.style.width = that.echartWidth + 'px'
            }

            // 将chart初始化的实例绑定到一个DOM
            this.chart = echarts.init(document.getElementById('gamechart'))
            this.chart.setOption(this.option)
        },
        // 参数container为图表盒子节点, charts为图表节点 
        chartssize (container,charts) {
            function getStyle(el, name) {　　
                if (window.getComputedStyle) {　　　
                    return window.getComputedStyle(el, null);　　
                } else {　　　
                    return el.currentStyle;　　
                }　
            }
            var wi = getStyle(container, 'width').width;
            var hi = getStyle(container, 'height').height;
            charts.style.width = wi
            // charts.style.height = hi
        }
    }
}
</script>

<style scoped>
.chart-container {
    position: relative;
    width: 100%;
    height: 500px;
}
.chart-container .ivu-echart {
    min-width: 100%;
    height: 500px;
}
</style>
```