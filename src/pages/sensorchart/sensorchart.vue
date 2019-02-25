<template>
  <div class="echarts-wrap">
    <div>12345</div>
    <mpvue-echarts :echarts="echarts" :onInit="onInit" canvasId="demo-canvas"/>
  </div>
</template>

<script>
import mpvueEcharts from 'mpvue-echarts';
import echarts from '../../../static/echarts.min';
/* eslint-disable  */

let chart = null;
var ipandaddr = null;
var freshaddr = null;
var item = null;
function initChart(canvas, width, height) {
  chart = echarts.init(canvas, null, {
    width,
    height
  });
  canvas.setChart(chart);
  return chart; // 返回 chart 后可以自动绑定触摸操作
}

export default {
  components: {
    mpvueEcharts
  },
  data() {
    return {
      echarts,
      onInit: initChart,
      ipandaddr: null,
      freshaddr: null
    };
  },
  onLoad(options) {
    this.ipandaddr = options.ipandaddr;
    wx.setNavigationBarTitle({
      title: `${options.addr}# ${options.name}`
    });
  },
  created() {
    wx.showLoading();
    setInterval(() => {
      if (chart != null) {
        if (this.ipandaddr != null) {
          if (this.ipandaddr != this.freshaddr) {
            wx.request({
              url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${
                this.ipandaddr
              }`,
              success: res => {
                var listx = [];
                var listy = [];
                for (var i = 0; i < res.data.length; i++) {
                  listx.push(res.data[i].time);
                  listy.push(res.data[i].listenvalue);
                }
                var option = {
                  color: ["#37A2DA"],
                  legend: {
                    data: ["71#"],
                    top: 50,
                    left: "center",
                    backgroundColor: "white",
                    z: 100
                  },
                  label: {
                    nomal: {
                      show: true
                    }
                  },
                  toolbox: {
                    show: true,
                    feature: {
                      mark: { show: true },
                      dataZoom: { show: true },
                      restore: { show: true }
                    }
                  },
                  calculable: true,
          tooltip: {
            trigger: 'axis',
            axisPointer: {
                type: 'cross'
            },
            formatter: '采集时间：{b0}\n监测值：{c0}',
            backgroundColor: 'rgba(245, 245, 245, 0.6)',
            borderWidth: 1,
            borderColor: '#ccc',
            padding: 10,
            textStyle: {
                color: '#000'
            },
            position: function (pos, params, el, elRect, size) {
                var obj = {top: 5};
                return obj;
            }
        },
                  dataZoom: [{
                    type: "slider",
                    realtime: true,
                    show: true,
                    start: 50,
                    end: 65,
                  }],
                  markLine: {
                    data: [
                      { type: "average", name: "平均值" },
                      { type: "max", name: "平均值" }
                    ]
                  },
                  xAxis: {
                    type: "category",
                    boundaryGap: false,
                    data: listx
                  },
                  yAxis: {
                    x: "center",
                    type: "value",
                    splitLine: {
                      lineStyle: {
                        type: "dashed"
                      }
                    }
                  },
                  series: [
                    {
                      name: "监测值",
                      type: "line",
                      data: listy,
                      color: "#4acd93",
                      smooth: true,
                      showAllSymbol: false,
                      symbolSize: function(value) {
                        return Math.round(value[2] / 10) + 2;
                      }
                    }
                  ]
                };
                chart.setOption(option);
                wx.hideLoading();
                this.freshaddr = this.ipandaddr;
              }
            });
          }
        }
      }
    }, 200);
  },
  onPullDownRefresh() {
    wx.request({
      url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipandaddr}`,
      success: res => {
        var listx = [];
        var listy = [];
        for (var i = 0; i < res.data.length; i++) {
          listx.push(res.data[i].time);
          listy.push(res.data[i].listenvalue);
        }
        var option = {
          color: ["#37A2DA"],
          legend: {
            data: ["71#"],
            top: 50,
            left: "center",
            backgroundColor: "white",
            z: 100
          },
          label: {
            nomal: {
              show: true
            }
          },
          toolbox: {
            show: true,
            feature: {
              mark: { show: true },
              dataZoom: { show: true },
              restore: { show: true }
            }
          },
          calculable: true,
           tooltip: {
            trigger: 'axis',
            axisPointer: {
                type: 'cross'
            },
            formatter: '采集时间：{b0}\n监测值：{c0}',
            backgroundColor: 'rgba(245, 245, 245, 0.8)',
            borderWidth: 1,
            borderColor: '#ccc',
            padding: 10,
            textStyle: {
                color: '#000'
            },
            position: function (pos, params, el, elRect, size) {
                var obj = {top: 5};
                //obj[['left'][+(pos[0] < size.viewSize[0] / 2)]] = 30;
                return obj;
            }
            // extraCssText: 'width: 170px'
        },
          dataZoom: [{
                    type: "slider",
                    realtime: true,
                    show: true,
                    start: 50,
                    end: 65
                  }],
          markLine: {
            data: [
              { type: "average", name: "平均值" },
              { type: "max", name: "平均值" }
            ]
          },
          xAxis: {
            type: "category",
            boundaryGap: false,
            data: listx
          },
          yAxis: {
            x: "center",
            type: "value",
            splitLine: {
              lineStyle: {
                type: "dashed"
              }
            }
          },
          series: [
            {
              name: "监测值",
              type: "line",
              data: listy,
              color: "#4acd93",
              smooth: true,
              showAllSymbol: false,
              symbolSize: function(value) {
                return Math.round(value[2] / 10) + 2;
              }
            }
          ]
        };
        chart.setOption(option);
        wx.hideLoading();
        this.freshaddr = this.ipandaddr;
      }
    });
    wx.stopPullDownRefresh();
  }
};
</script>
<style lang="less" scoped>
@import "../../styles/mixin";
.tr {
  display: flex;
  align-items: left; /*垂直居中*/
  box-shadow: 0 0 10rpx rgb(0, 0, 0);
  margin-top: 10rpx;
  margin-left: 10rpx;
  margin-right: 10rpx;
}
.contain3 {
  display: flex;
  align-items: center; /*垂直居中*/
  font-size: 30rpx;
  margin-top: 10rpx;
}
.contains4 {
  display: flex;
  align-items: center; /*垂直居中*/
  font-size: 30rpx;
  margin-bottom: 10rpx;
}
.icon {
  width: 96rpx;
  height: 96rpx;
  box-shadow: 0 0 15px rgb(15, 112, 141) inset, 0 0 5px rgb(15, 112, 141);
  margin-top: 5rpx;
  margin-bottom: 5rpx;
  margin-left: 5rpx;
}
.icon1 {
  width: 32rpx;
  height: 32rpx;
  margin-left: 5rpx;
}
.icon2 {
  width: 32rpx;
  height: 32rpx;
  margin-left: 35rpx;
}
.icon3 {
  width: 32rpx;
  height: 32rpx;
  margin-left: 5rpx;
}
.echarts-wrap {
  width: 100%;
  height: 600rpx;
}
</style>
