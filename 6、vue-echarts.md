# Echart

[介绍](https://www.jianshu.com/p/8cac22daca98) --|-- [详细](https://www.cnblogs.com/1ming/p/9547947.html)

[官方实例地址](https://www.echartsjs.com/examples/zh/index.html#chart-type-pie)

- 用法：_that.$chart.lineChina('chart', newArr);

## 安装

```js
npm install --s echarts

// 组件局部引入
import echarts from 'echarts'
```

---

## 方式一

```ts
const echarts = require('echarts');

/**
 * 图标折叠
 * @id 挂载的DIV标签
 * @xAxisName 名称
 * @xAxisData X轴数据
 * @seriesFoldData 数据
 */
const install = function (Vue: any) {
  Object.defineProperties(Vue.prototype, {
    $chart: {
      get() {
        return {
          lineFold: function (id: string, xAxisData: object, xAxisName: string, seriesFoldData: object) {  // 折线图
            if (document.getElementById(id) == null) {
              return
            }
            echarts.dispose(document.getElementById(id));
            this.chart1 = echarts.init(document.getElementById(id));
            this.chart1.clear();

            const optionData = {
              title: {
                text: '',
                x: 'center',
                top: 0,
              },
              tooltip: { // 提示框
                trigger: 'axis',
                axisPointer: {
                  type: 'cross',
                  label: {
                    backgroundColor: '#6a7985'
                  }
                }
              },
              legend: {
                x: 'center',      // 可设定图例在左、右、居中
                y: 'top',        // 可设定图例在上、下、居中,
                top: 20,
                data: [xAxisName]
              },
              toolbox: {
                orient: 'horizontal',
                show: false,      // 是否显示工具栏组件
                x: 'left',
                feature: {
                  saveAsImage: {}
                }
              },
              grid: {
                left: '15px',
                right: '15px',
                bottom: '0%',
                containLabel: true
              },
              xAxis: [
                {
                  type: 'category',
                  boundaryGap: false,
                  axisLabel: {
                    show: true,
                    textStyle: {
                      color: '#899daf',  //更改坐标轴文字颜色
                      fontSize: 12      //更改坐标轴文字大小
                    }
                  },
                  axisTick: {
                    show: true
                  },
                  axisLine: {
                    lineStyle: {
                      color: '#899daf' //更改坐标轴颜色
                    }
                  },
                  data: xAxisData
                }
              ],
              yAxis: [
                {
                  type: 'value',
                  // interval: 200, // 设置左侧网格值
                  splitLine: {    // 设置网格线颜色
                    show: true,   // 隐藏网格线
                    lineStyle: {
                      color: ['rgb(235, 235, 235)'],
                      width: 1,
                      type: 'solid'
                    }
                  },
                  axisLabel: {
                    show: true,
                    textStyle: {
                      color: '#899daf',  //更改坐标轴文字颜色
                      fontSize: 12      //更改坐标轴文字大小
                    }
                  },
                  axisTick: {
                    show: true
                  },
                  axisLine: {
                    lineStyle: {
                      color: '#899daf' //更改坐标轴颜色
                    }
                  }
                }
              ],
              series: [  // 数据源
                {
                  name: xAxisName,
                  type: 'line',
                  stack: '总量',
                  smooth: 0.3,    // 线条曲线
                  itemStyle: {
                    normal: {
                      color: '#409eff',   // 改变折线点的颜色
                      lineStyle: {
                        color: '#409eff' // 改变折线颜色
                      }
                    }
                  },
                  areaStyle: {
                    normal: { color: 'transparent', }  // 改变区域颜色
                  },
                  data: seriesFoldData
                },
              ]
            };
            this.chart1.setOption(optionData);
            this.chart1.resize();
          },
          lineDoughnut: function (id: string, seriesFoldData: any) {  // 饼状图
            if (document.getElementById(id) == null) {
              return
            }
            echarts.dispose(document.getElementById(id));
            this.chart = echarts.init(document.getElementById(id) as any);
            this.chart.clear();

            const optionData = {
              tooltip: {
                trigger: 'item'
              },
              legend: {
                top: '5%',
                left: 'center'
              },
              color : [ '#2d8bec', ],
              series: [
                {
                  name: '平台数据',
                  type: 'pie',
                  radius: ['40%', '70%'],
                  avoidLabelOverlap: false,
                  label: {
                    show: false,
                    position: 'center'
                  },
                  emphasis: {
                    label: {
                      show: true,
                      fontSize: '16',
                      fontWeight: 'bold'
                    }
                  },
                  labelLine: {
                    show: false
                  },
                  data: seriesFoldData
                }
              ]
            };
            this.chart.setOption(optionData);
          },
        }
      }
    },
  })
}

export default {
  install
}
```

### main 全局引入

```js
import myECharts from "./components/Echarts/echarts";   // 挂载图表

Vue.use(myECharts);
```

---

## 方式二

### 1、新建cakechart.js，引用 echarts官网 中具体的实例

```js
/**  新建 cakechart.js 饼状图
 */
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
    /**
     * toolbox -下载按钮   show: true | false
     */
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

## 2、在组件模板中引入JS，并将其挂载到页面上

- 1、首先引入 `cakechart.js` 文件

- 2、created() 挂载时加载 fetchData() 数据，防止刷新页面会丢失数据的情况

- 3、通过 fetchData() 在拿到后台数据后，执行 initChart() 设置处理在刷新时表格的宽度问题

### `问题： 1、遇到如何解决 echart 宽高自适应的问题？`

- **解决方案：**  `-【指的是数据 data 已经初始化完成，方法也已经可以调用，但是 DOM未渲染。】` created()- 在挂载DOM前拿到父节点（echartBox）的宽度，因为还没挂载DOM，此时拿到宽度（offsetWidth）浏览器会报错，所以利用 setTimeout() 延迟去拿宽度，这样浏览器不会报错；

---

### 代码

```vue
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
// 安装 echarts 后，直接引入库
import echarts from 'echarts';
import { option } from './cakechart';

export default {
    data() {
        return {
            chart: {},
            option: option,
            echartWidth: ''  // 默认宽度
        }
    },
    created() {
        let that = this
        that.fetchData()
        setTimeout(() => {
            // 获取宽度
            let chartBox = document.getElementById('echartBox');
            that.echartWidth = chartBox.offsetWidth;
            console.log(chartBox.offsetWidth);  // 1920
        }, 300)
    },
    watch: {
        "echartWidth": {
            handler: function(newer, older) {
                if (newer == null) {
                    return
                } else {
                    let that = this
                    console.log(newer);
                }
            },
        　　deep: true
        }
    },
    // 挂载图表函数
    mounted() {
        let that = this
        // that.initChart()
    },
    // 方法
    methods: {
        /**
         *  拿后台数据
         */
        fetchData() {
            let that = this
            that.chart = {
                lastday: [
                    { value: 10, name: '平台用户总人数' },
                    { value: 5, name: '本月新增用户' },
                    { value: 15, name: '今日新增用户' }
                ],
                lastdayData: [
                    { value: 10, name: '养分出售总额' },
                    { value: 5, name: '养分回收爱心值' },
                    { value: 15, name: '正使用养分总额' }
                ]
            }
            that.option.series[0].data = that.chart.lastday
            that.option.series[1].data = that.chart.lastdayData
            /**
            *  获取到数据后再进行渲染，DOM挂载数据
            */
            that.initChart()
        },
        /**
         *  可以动态设置宽度
         */
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
            /**
            *  挂载 -将chart初始化的实例绑定到一个DOM
            */
            this.chart = echarts.init(document.getElementById('gamechart'))
            this.chart.setOption(this.option)
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
