<template>
  <div class="echarts-wrap">
    <mpvue-echarts :echarts="echarts" :onInit="onInit" canvasId="demo-canvas"/>
  </div>
</template>

<script>
import mpvueEcharts from 'mpvue-echarts';
import echarts from '../../../static/echarts.min';

/* eslint-disable  */

let chart = null;

function initChart(canvas, width, height) {
  chart = echarts.init(canvas, null, {
    width,
    height
  });
  canvas.setChart(chart);
  this.echarts = chart;
  const option = {
    title: {
      text: "温度传感器历史数据",
      left: "center"
    },
    color: ["#37A2DA"],
    legend: {
      data: ["71#"],
      top: 50,
      left: "center",
      backgroundColor: "white",
      z: 100
    },
    grid: {
      containLabel: true
    },
    label: {
      nomal: {
        show: true
      }
    },
    tooltip: {
      show: true,
      trigger: "axis",
      axisPointer: {
        type: "cross",
        axis: "x"
      }
    },
    markLine: {
      data: [
        { type: "average", name: "平均值" },
        { type: "max", name: "平均值" }
      ]
    },
    xAxis: {
      type: "category",
      boundaryGap: false,
      data: []
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
        name: this.ipandaddr,
        type: "line",
        smooth: false,
        data: []
      }
    ]
  };

  chart.setOption(option);

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
      ipandaddr: "",
      listx: [],
      listy: []
    };
  },
  onLoad(options) {
    this.ipandaddr = options.ipandaddr;
    wx.setNavigationBarTitle({
      title: `监测曲线`
    });
    wx.showLoading();
    wx.request({
      url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipandaddr}`,
      success: res => {
        this.listx = [];
        this.listy = [];
        for (var i = 0; i < res.data.length; i++) {
          this.listx.push(res.data[i].time);
          this.listy.push(res.data[i].listenvalue);
        }
        const option = {
          title: {
            text: "温度传感器历史数据",
            left: "center"
          },
          color: ["#37A2DA"],
          legend: {
            data: ["71#"],
            top: 50,
            left: "center",
            backgroundColor: "white",
            z: 100
          },
          grid: {
            containLabel: true
          },
          label: {
            nomal: {
              show: true
            }
          },
          grid: {
            y2: 80
          },
          toolbox: {
            show: true,
            feature: {
              mark: { show: true },
              dataView: { show: true, readOnly: false, height: 120 },
              magicType: { show: true, type: ["line", "bar"] },
              restore: { show: true },
              saveAsImage: { show: true }
            }
          },
          calculable: true,
          tooltip: {
            trigger: "axis",
            axisPointer: {
              show: true,
              type: "cross",
              lineStyle: {
                type: "dashed",
                width: 1
              }
            }
          },
          dataZoom: {
            show: true,
            start: 70
          },
          markLine: {
            data: [
              { type: "average", name: "平均值" },
              { type: "max", name: "平均值" }
            ]
          },
          xAxis: {
            type: "category",
            boundaryGap: false,
            data: this.listx
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
              data: this.listy,
              // itemStyle: {normal: {areaStyle: {type: 'default'}, color:'#00C5CD'}},
              color: "#4acd93",
              smooth: true,
              showAllSymbol: true,
              symbolSize: function(value) {
                return Math.round(value[2] / 10) + 2;
              }
            }
          ]
        };
        chart.setOption(option);
        wx.hideLoading();
      }
    });
  },
  onPullDownRefresh() {
     wx.stopPullDownRefresh();
  }
};
</script>
<style scoped>
.echarts-wrap {
  width: 100%;
  height: 300px;
}
</style>
