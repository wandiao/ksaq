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
    color: ["#37A2DA", "#67E0E3", "#9FE6B8"],
    legend: {
      data: ['71#', '72#', '74#'],
      top: 50,
      left: 'center',
      backgroundColor: 'white',
      z: 100
    },
    grid: {
      containLabel: true
    },
    tooltip: {
      show: true,
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: ['00:00', '01:00', '02:00', '03:00', '04:00', '05:00', '06:00','07:00', '08:00', '09:00', '10:00', '11:00', '12:00', '13:00', '14:00', '15:00', '16:00', '17:00', '18:00', '19:00', '20:00', '21:00', '22:00', '23:00'],
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
      data: [18.0, 18.2, 18.5, 18.7, 18.8, 18.8, 
      19.2, 19.4, 19.3, 19.6, 19.8, 19.9, 
      21.3, 21.6, 21.8, 22.6, 22.5, 22.3, 
      21.6, 21.4, 21.2, 19.6, 18.5, 18.4]
    }, {
      name: '72#',
      type: 'line',
      smooth: false,
      data: [18.3, 18.2, 18.8, 18.7, 18.8, 18.8, 
      18.6, 18.0, 18.6, 18.5, 18.7, 22.5, 
      18.8, 18.6, 18.0, 18.9, 21.5, 18.7, 
      18.8, 18.8, 18.6, 18.9, 21.6, 22.5]
    }, {
      name: '74#',
      type: 'line',
      smooth: false,
      data: [12.3, 12.6, 13.0, 13.4, 14.0, 14.5, 
      14.6, 14.8, 15.2, 15.6, 15.9, 16.5, 
      17.1, 17.8, 17.0, 16.5, 16.0, 15.5, 
      15.3, 15.0, 14.3, 14.0, 12.2, 12.0]
}]
  };

export default {
  data () {
    return {
      ec: {
        // 传 options
        options: options
      }
    }
  }
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
