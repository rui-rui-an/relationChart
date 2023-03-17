<template>
  <div class="home">
    <div id="mychart">

    </div>
  </div>
</template>

<script>
import graph from './data'
import * as echarts from 'echarts'
let categories = graph.nodes.map(function (a) {
  // console.log(a);
  return a.name;
})
console.log(categories);
// let a = graph.categories.map(function (a) {
//               return a.name;
//             })
//             console.log(a);
export default {
  data () {
    return {
      mychart: null
    }
  },
  mounted () {
    this.initEcharts()
  },
  methods: {
    initEcharts () {
      const _this = this
      const chartDom = document.getElementById('mychart')
      this.mychart = echarts.init(chartDom)
      const option = {
        title: {
          text: 'Les Miserables',
          subtext: 'Default layout',
          top: 'bottom',
          left: 'right'
        },
        tooltip: {},
        legend: [
          {
            data: graph.categories.map(function (a) {
              return a.name;
            })
          }
        ],
        series: [
          {
            type: 'graph',// 类型:关系图
            layout: 'force',//图的布局，类型为力导图
            edgeSymbol: ['circle', 'arrow'],
            edgeSymbolSize: [2, 8], // 两端大小
            data: graph.nodes,
            links: graph.links,
            categories: graph.categories,
            roam: true,
            label: {
              position: 'right'
            },
            edgeLabel: {
              position: 'middle', // 标签位置 线的中点
              show: true,
              fontSize: 12,
              formatter: (param) => {
                return param.data.dataRelationShipName
              }
            },
            force: {
              repulsion: 300,// 两个节点之间的距离
              edgeLength: 300,//节点之间的斥力因子值
              friction: 0.5, // 节点的移动速度 取值0~1
              layoutAnimation: true
            },
            roam: true, //是否开启鼠标缩放和平移漫游。默认不开启。如果只想要开启缩放或者平移,可以设置成 'scale' 或者 'move'。设置成 true 为都开启
            draggable: true, //每个节点的拖拉
            cursor: 'pointer',
            emphasis: {
              scale: true, //是否开启鼠标悬停节点的显示动画
              lineStyle: {
                width: 2
              },
              shadowColor: '#00FAE1',
              shadowOffsetX: 0,
              shadowOffsetY: 0,
              shadowBlur: 40,
              focus: 'adjacency'
            },
             //图形上的文本标签，可用于说明图形的一些数据信息
             label: {
              //标签的字体样式
              fontStyle: 'normal', //文字字体的风格 'normal'标准 'italic'斜体 'oblique' 倾斜
              fontWeight: 'bolder', //'normal'标准'bold'粗的'bolder'更粗的'lighter'更细的或100 | 200 | 300 | 400...
              fontFamily: 'sans-serif', //文字的字体系列
              fontSize: 12, //字体大小
              show: true, //显示
              position: 'top', //相对于节点标签的位置，默认在节点中间
              //回调函数，你期望节点标签上显示什么
              formatter: function (params) {
                return params.name
              }
            },
          }
        ]
      };
      this.mychart.setOption(option, false);
      this.updatePositon()
      this.mychart.on('dblclick', (params) => {
        this.mychart.showLoading()
        // 模拟发送接口返回新的数据
        setTimeout(() => {
          let mockData = {
            "id": this.getRandomNum(6, 10000).toString(),
            "name": "我是节点" + this.getRandomNum(6, 10000),
            "symbolSize": this.getRandomNum(30, 70),
            "value": 2,
            "category": this.getRandomNum(0, 4)
          }
          graph.nodes.push(mockData)
          graph.links.push({
            "source": params.data.id,
            "target": mockData.id,
            'dataRelationShipName': this.getRandomNum(6, 10000) + '关系'
          })
          // const option = { ..._this.mychart.getOption()}
          this.mychart.setOption(option, false)
          this.updatePositon()
          this.mychart.hideLoading()
        }, 500)
      });

      this.mychart.on('mouseup', (params) => {
        const option = { ..._this.mychart.getOption() }
        this.mychart.setOption(option, false)
      })


    },
    updatePositon () {
      setTimeout(() => {
        let model = this.mychart
          .getModel()
          .getSeriesByIndex(0)
          .getData()._itemLayouts
        if (model.length) {
          graph.nodes.forEach((item, index) => {
            item.x = model[index][0]
            item.y = model[index][1]
            item.fixed = true
          })
        }
      }, 1000)
    },
    getRandomNum (min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    }
  }
}
</script>
<style lang="less">
.home {
  #mychart {
    width: 100%;
    height: 90vh;
  }
}
</style>