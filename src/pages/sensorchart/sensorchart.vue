<template>
  <div>
    <div class="contain">
      <i-row>
        <i-col span="4">
          <img class="icon" :src="iconMap[name]">
        </i-col>
        <i-col span="20">
          <i-row>
            <i-tag class="i-tags" name="1" color="yellow">{{addr}}#{{name}}</i-tag>
          </i-row>
          <i-row>
            <i-tag class="i-tags" name="1" :color="iconMap[link]">{{link}}</i-tag>
            <i-tag class="i-tags" name="1" :color="iconMap[can]">{{can}}</i-tag>
            <i-tag class="i-tags" name="1" color="green">{{time}}</i-tag>
            <i-tag class="i-tags" name="1" color="yellow">{{value}}</i-tag>
          </i-row>
        </i-col>
      </i-row>
    </div>
    <div class="echarts-wrap1">
      <i-row>
        <i-col offset="10" span="14"><i-tag class="i-tags" name="1" color="green">实时曲线</i-tag></i-col>
      </i-row>
      <mpvue-echarts :echarts="echarts" :onInit="curInit" canvasId="lin1"/>
    </div>
    <div class="echarts-wrap">
      <i-row>
        <i-col offset="10" span="14"><i-tag class="i-tags" name="1" color="yellow">历史曲线</i-tag></i-col>
      </i-row>
      <mpvue-echarts :echarts="echarts" :onInit="logInit" canvasId="line"/>
    </div>
  </div>
</template>

<script>
import mpvueEcharts from 'mpvue-echarts';
import echarts from '../../../static/echarts.min';
/* eslint-disable  */
const iconMap = {
  烟雾: "/static/images/common/smog.png",
  开停: "/static/images/common/switch.png",
  温湿度: "/static/images/common/tempth.png",
  负压: "/static/images/common/vol.png",
  断电器: "/static/images/common/breaker.png",
  激光甲烷: "/static/images/common/line.png",
  甲烷高低浓: "/static/images/common/line.png",
  二氧化碳: "/static/images/common/co2.png",
  风速: "/static/images/common/speed.png",
  读卡器: "/static/images/common/reader.png",
  甲烷低浓度: "/static/images/common/ch4.png",
  广播终端: "/static/images/common/boardcast.png",
  氧气: "/static/images/common/o2.png",
  一氧化碳: "/static/images/common/co.png",
  粉尘: "/static/images/common/drug.png",
  语音风门: "/static/images/common/fengmen.png",
  转换器: "/static/images/common/switcher.png",
  风筒: "/static/images/common/fengtong.png",
  电源: "/static/images/common/power.png",
  CAN1: "blue",
  CAN2: "blue",
  CAN3: "blue",
  CAN4: "blue",
  "": "red",
  正常: "green",
  中断: "red",
  未知: "/static/images/common/linkbreak.png",
  等待连接: "/static/images/common/waitlink.png",
  综合分站: "/static/images/common/station.png",
  ipicon: "/static/images/common/ipicon.png",
  codeicon: "/static/images/common/code.png",
  nameicon: "/static/images/common/name.png",
  infoicon: "/static/images/common/infoicon.png",
  positionicon: "/static/images/common/position.png",
  time: "/static/images/common/time.png"
};
let chart, curchart;
var curx = [],
  cury = [];
var listx = [],
  listy = [];
var res;
var unit = "%";
function getBarOption() {
  return {
    grid: {
      left: 15,
      right: 15,
      bottom: 60,
      top: 10,
      containLabel: true
    },
    backgroundColor: "", //背景颜色透明
    calculable: true,
    tooltip: {
      trigger: "axis",
      formatter: `采集时间：{b0}\n监测值：{c0}${unit}`,
      backgroundColor: "rgba(238, 180, 34, 0.6)",
      borderWidth: 3,
      borderColor: "#ccc",
      textStyle: {
        color: "#FFF"
      },
      axisPointer: {
        show: true,
        type: "line",
        lineStyle: {
          color: "#FF7F00",
          type: "line",
          width: 1
        },
        label: {
          show: false
        }
      },
      position: function(point, params, dom, rect, size) {
        if (point[0] > size.viewSize[0] / 2)
          return [point[0] - 135, point[1] - 100];
        else return [point[0] + 20, point[1] - 100];
      }
    },
    dataZoom: [
      {
        type: "slider",
        left:"25",
        bottom:"30",
        realtime: true,
        backgroundColor: 'rgba(238,180,34,0)',
        show: true,
        start: 0,
        end: 100,
        handleIcon:"M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z",
        handleSize: "100%",
        handleStyle: {
          color: "#fff",
          shadowBlur: 3,
          shadowColor: "rgba(0, 0, 0, 0.3)",
          shadowOffsetX: 2,
          shadowOffsetY: 2
        }
      }
    ],
    xAxis: {
      type: "category",
      boundaryGap: true,
      data: listx,
      show: true,
      axisLine:{
        lineStyle:{
        color:'#FF7F00',
        }
     } 
    },
    yAxis: {
      x: "center",
      type: "value",
      show: true,
      "splitLine": {     //网格线
          "show": false
        },
        scale: true,
      axisLine:{
        lineStyle:{
        color:'#FF7F00',
        }
     }
    },
    series: [
      {
        name: "监测值",
        type: "line",
        data: listy,
        smooth: true,
        showAllSymbol: false,
        itemStyle: {
          normal: {
            //颜色渐变函数 前四个参数分别表示四个位置依次为左、下、右、上
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              {
                offset: 0,
                color: "#FFEBCD" // 0% 处的颜色
              },
              {
                offset: 0.3,
                color: "#FFD39B" // 100% 处的颜色
              },
              {
                offset: 1,
                color: "#fff" // 100% 处的颜色
              }
            ]), //背景渐变色
            lineStyle: {
              // 系列级个性化折线样式
              width: 2,
              type: "solid",
              color: "#FF7F00"
            }
          },
          emphasis: {
            color: "#FF7F00",
            lineStyle: {
              // 系列级个性化折线样式
              width: 2,
              type: "dotted",
              color: "#FF7F00" //折线的颜色
            }
          }
        }, //线条样式
        symbolSize: 0.001, //折线点的大小
        areaStyle: { normal: {} }
      }
    ]
  };
}
export default {
  components: {
    mpvueEcharts
  },
  data() {
    return {
      echarts,
      iconMap,
      logInit: function(canvas, width, height) {
        chart = echarts.init(canvas, null, {
          width: width,
          height: height
        });
        canvas.setChart(chart);
        return chart;
      },
      curInit: function(canvas, width, height) {
        curchart = echarts.init(canvas, null, {
          width: width,
          height: height
        });
        canvas.setChart(curchart);
        return curchart;
      },
      ipandaddr: null,
      freshaddr: null,
      link: "中断",
      name: "风速",
      can: "CANNONE",
      value: "",
      addr: "",
      fresh: null,
      ip: "",
      time: ""
    };
  },
  onLoad(options) {
    this.ipandaddr = options.ipandaddr;
    this.addr = options.addr;
    this.name = options.name;
    this.fresh = "1";
    this.ip = options.ip;
    wx.setNavigationBarTitle({
      title: "传感器历史数据"
    });
  },
  created() {
    wx.showLoading();
    setInterval(() => {
      if (this.ipandaddr != null && this.fresh != null) {
        wx.request({
          url: `https://api.zouyang.ltd/sensorcurinfo?ipandaddr=${
            this.ipandaddr
          }&ip=${this.ip}`,
          success: res => {
            this.name = res.data.name;
            this.link = res.data.link;
            this.can = res.data.can;
            this.value = res.data.value;
            this.addr = res.data.addr;
            this.time = res.data.time;
            if (curx.length >= 1800) {
              curx.splice(0, 1);
              cury.splice(0, 1);
            }
            curx.push(res.data.time);
            cury.push(res.data.listenvalue);
            var option1 = {
              grid: {
                left: 15,
                right: 15,
                bottom: 25,
                top: 15,
                containLabel: true
              },
              backgroundColor: "", //背景颜色透明
              label: {
                nomal: {
                  show: true
                }
              },
              calculable: true,
              tooltip: {
                trigger: "axis",
                formatter: `采集时间：{b0}\n监测值：{c0}${unit}`,
                backgroundColor: "rgba(72, 209, 204, 0.6)",
                borderWidth: 3,
                borderColor: "#ccc",
                textStyle: {
                  color: "#FFF"
                },
                axisPointer: {
                  show: true,
                  type: "line",
                  lineStyle: {
                    color: "#4fd6d2",
                    type: "line",
                    width: 1
                  },
                  label: {
                    show: false
                  }
                },
                position: function(point, params, dom, rect, size) {
                  if (point[0] > size.viewSize[0] / 2)
                    return [point[0] - 135, point[1] - 100];
                  else return [point[0] + 20, point[1] - 100];
                }
              },
              xAxis: {
                type: "category",
                boundaryGap: true,
                data: curx,
                show: true,
                axisLine:{
                  lineStyle:{
                  color:'#00C5CD',
                  }
                }
              },
              yAxis: {
                x: "center",
                "splitLine": {     //网格线
          "show": false
        },
                type: "value",
                show: true,
                scale: true,
                axisLine:{
                  lineStyle:{
                  color:'#00C5CD',
                  }
                }
              },
              series: [
                {
                  name: "监测值",
                  type: "line",
                  data: cury,
                  smooth: true,
                  showAllSymbol: false,
                  itemStyle: {
                    normal: {
                      //颜色渐变函数 前四个参数分别表示四个位置依次为左、下、右、上
                      color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                        {
                          offset: 0,
                          color: "#d7f4f8" // 0% 处的颜色
                        },
                        {
                          offset: 0.3,
                          color: "#eefcfd" // 100% 处的颜色
                        },
                        {
                          offset: 1,
                          color: "#fff" // 100% 处的颜色
                        }
                      ]), //背景渐变色
                      lineStyle: {
                        // 系列级个性化折线样式
                        width: 2,
                        type: "solid",
                        color: "#4fd6d2"
                      }
                    },
                    emphasis: {
                      color: "#4fd6d2",
                      lineStyle: {
                        // 系列级个性化折线样式
                        width: 2,
                        type: "dotted",
                        color: "#4fd6d2" //折线的颜色
                      }
                    }
                  }, //线条样式
                  symbolSize: 0.001, //折线点的大小
                  areaStyle: { normal: {} }
                }
              ]
            };
            if (curchart != null) 
            {
              curchart.setOption(option1);
            }
          }
        });
      }
    }, 1000);
    setInterval(() => {
      if (chart != null) {
        if (this.ipandaddr != null && this.fresh != null) {
          if (this.ipandaddr != this.freshaddr) {
            wx.request({
              url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${
                this.ipandaddr
              }`,
              success: res => {
                var listx1 = [];
                var listy1 = [];
                for (var i = 0; i < res.data.length; i++) {
                  listx1.push(res.data[i].time);
                  listy1.push(res.data[i].listenvalue);
                }
                listx = listx1;
                listy = listy1;
                unit = res.data[0].unit;
                chart.setOption(getBarOption());
                this.freshaddr = this.ipandaddr;
                wx.hideLoading();
              }
            });
          }
        }
      }
    }, 100);
  },
  onUnload() {
    this.ipandaddr = null;
    this.freshaddr = null;
    this.fresh = null;
    curx = [];
    cury = [];
  },
  onPullDownRefresh() {
    curx = [];
    cury = [];
    wx.request({
      url: `https://api.zouyang.ltd/sensorinfologs?ipandaddr=${this.ipandaddr}`,
      success: res => {
        var listx1 = [];
        var listy1 = [];
        for (var i = 0; i < res.data.length; i++) {
          listx1.push(res.data[i].time);
          listy1.push(res.data[i].listenvalue);
        }
        listx = listx1;
        listy = listy1;
        unit = res.data[0].unit;
        chart.setOption(getBarOption());
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
.echarts-wrap {
  margin-top: 25rpx;
  margin-left: 15rpx;
  width: 96%;
  height: 490rpx;
  box-shadow:  0 0 10px rgb(187, 97, 13);
}
.echarts-wrap1 {
  margin-top: 15rpx;
  width: 96%;
  margin-left: 15rpx;
  height: 450rpx;
  box-shadow:  0 0 10px rgb(10, 155, 148);
}
.icon {
  width: 105rpx;
  height: 105rpx;
  box-shadow: 0 0 15px rgb(15, 112, 141) inset, 0 0 5px rgb(15, 112, 141);
}
.icon1 {
  width: 55rpx;
  height: 55rpx;
  box-shadow: 0 0 15px rgb(15, 112, 141) inset, 0 0 5px rgb(15, 112, 141);
  margin-top: 5rpx;
}
.contain {
  margin-top: 20rpx;
  box-shadow: 0 0 5px rgb(50, 52, 53);
}
</style>
