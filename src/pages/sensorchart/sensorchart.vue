<template>
  <div class="counter-warp">
    <div class="container df-ct">
      <ec-canvas class="canvas" id="mychart-dom-bar" canvas-id="mychart-bar" :ec="ec"></ec-canvas>
    </div>
  </div>
</template>

<script>
/* eslint-disable  */
const options = {
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
      name: '71#',
      type: 'line',
      smooth: false,
      data: this.listy
    }]
  };

export default {
  data () {
    return {
      ec: {
        // 传 options
        options: options
      },
      ipandaddr,
      list: [],
      listx: [],
      listy: [],
    }
  },
    onLoad(options) {
      this.ipandaddr = options.ipandaddr;
      wx.setNavigationBarTitle({
        title: `监测曲线`,
      });
    },
    onPullDownRefresh() {
      wx.showLoading();
      wx.request({
        url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipipandaddrandaddr}`,
        success: (res) => {
          console.log(res);
          wx.hideLoading();
          this.listx = null;
          this.listy = null;
          this.list = res.data;
          for(var i=0;i<this.list.length;i++){
              listx.push(this.list[i].time);
              listy.push(this.lst[i].listenvalue);
          }
          wx.stopPullDownRefresh();
        },
      });
    },
    created() {
        wx.showLoading();
        wx.request({
        url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipipandaddrandaddr}`,
        success: (res) => {
          console.log(res);
          wx.hideLoading();
          this.listx = null;
          this.listy = null;
          this.list = res.data;
          for(var i=0;i<this.list.length;i++){
              listx.push(this.list[i].time);
              listy.push(this.lst[i].listenvalue);
          }
          wx.hideLoading();
        },
      });
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
