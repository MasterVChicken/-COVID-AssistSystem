<template>
  <div id="mapchart" style="width:100%; height: 600px;"></div>
</template>

<script>
import jsonp from 'jsonp';
import echarts from 'echarts'
import 'echarts/map/js/china';

const option = {
  title: {
    text: '全国疫情实时地图',
    show: true,
    x: 'center',
    top: '5%',
    textStyle: {
      color: '#e74c3c',
      fontStyle: 'normal',
      fontFamily: 'Microsoft YaHei'
    }
  },
  tooltip: {
    //  鼠标移入时的提示信息
    // 类型
    tigger: 'item',
    // b 区域名称 c 合并数值 a 系列名称
    formatter: '地区：{b}<br/>确诊：{c} '
  },
  series: [
    {
      type: 'map',//地图类型
      map: 'china',//地图名字（与导入文件名字一致
      layoutCenter: ['50%', '50%'],//定义地图中心在屏幕中的位置
      layoutSize: 650,
      label: {
        show: true,
        color: '#000',
        fontSize: '10px'
      },
      itemStyle: {
        borderColor: '#b1b1b1'
      },
      zoom: 1,
      emphasis: {
        lable: {
          color: '#fff',
          fontSize: '10px'
        },
        itemStyle: {
          areaColor: '#c7fffd'
        }
      }
    }
  ],
  visualMap: {
    type: 'piecewise',
    pieces: [
      {min: 10000}, // 不指定 max，表示 max 为无限大（Infinity）。
      {min: 1000, max: 9999},
      {min: 100, max: 999},
      {min: 10, max: 99},
      {min: 0, max: 9}
    ],
    inRange: {
      color: ['#fff', '#ffaa85', '#ff7b69', '#cc2929', '#8c0d0d', '#660208']
    },
    itemWidth: 10,
    itemHeight: 10,
    bottom: '15%',
    left: '10%'
  }
}
export default {
  name: 'virusMap',
  data() {
    return {
      mycharts: '',
    }
  },
  mounted() {
    this.mycharts = echarts.init(document.getElementById('mapchart'))
    this.init()
  },
  methods: {
    init() {
      // 使用新浪疫情数据接口获取数据
      jsonp(
        'http://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',
        (err, data) => {
          //console.log(data.data.list);
          const list = data.data.list.map(item => {
            return {
              name: item.name,
              value: item.value
            }
          })

          // 将数据给到地图
          option.series[0].data = list;

          this.mycharts.setOption(option);
        })
    }
  }
}
</script>

<style scoped>

</style>
