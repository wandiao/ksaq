<template>
  <div class="counter-warp">
    <div class="container df-ct">
      <ec-canvas class="canvas" id="mychart-dom-line" canvas-id="mychart-bar" :ec="ec"></ec-canvas>
    </div>
  </div>
</template>

<script>
import Chart from '../../../static/ec-canvas/echarts';
/* eslint-disable  */
var options = {
    title: {
      text: '温度传感器历史数据',
      left: 'center'
    },
    color: ["#37A2DA"],
    legend: {
      data: ['71#'],
      top: 50,
      left: 'center',
      backgroundColor: 'white',
      z: 100
    },
    grid: {
      containLabel: true
    },
    label:{
      nomal:{
        show:true
      }
    },
    tooltip: {
      show: true,
      trigger: 'axis',
      axisPointer: {
        type: 'cross',
        axis: "x",
      }
    },
    markLine:{
      data: [
        {type: 'average',name: '平均值'},
        {type: 'max',name: '平均值'}
      ]
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: this.listx,
    },
    yAxis: {
      x: 'center',
      type: 'value',
      splitLine: {
        lineStyle: {
          type: 'dashed'
        }
      }
    },
    series: [{
      name: this.ipandaddr,
      type: 'line',
      smooth: false,
      data: this.listy
    }]
  };
 function init() {
    this.chart = Chart.init(this.$ec-canvas.canvas);
    this.chart.setOption(this.option);
  }
export default {
  data () {
    return {
      ipandaddr: '',
      listx: ['1','2','3'],
      listy: [1,2,3],
      chart: '',
      ec: {
        // 传 options
        options: options
      }
    }
  },
  methods: {
 
  },
    onLoad(options) {
      this.ipandaddr = options.ipandaddr;
      wx.setNavigationBarTitle({
        title: `监测曲线`,
      });
      wx.showLoading();
        wx.request({
        url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipandaddr}`,
        success: (res) => {
          wx.hideLoading();
          for(var i=0;i<res.data.length;i++){
              this.listx.push(res.data[i].time);
              this.listy.push(res.data[i].listenvalue);
          }
          wx.hideLoading();
        },
      });
    },
    onPullDownRefresh() {
      var that = this;
      wx.showLoading();
      wx.request({
        url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipandaddr}`,
        success: (res) => {
          wx.hideLoading();
          for(var i=0;i<res.data.length;i++){
              that.listx.push(res.data[i].time);
              that.listy.push(res.data[i].listenvalue);
          }
          init();
          wx.hideLoading();
        },
      });
    },
    created() {
        
    },
}
</script>

<style lang="less">
.df-ct {
  height:100%;
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:space-between;
  padding:200rpx 0;
  box-sizing:border-box;

}
ec-canvas {
  width: 100%;
  height: 800rpx;
}
</style>
