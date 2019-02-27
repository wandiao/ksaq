<template>
  <div class="echarts-wrap">
      <div plain="true" size="mini" class="tr">
        <div class="contain1" ><img class="icon" :src="iconMap[name]" float:left /></div>
        <div class="contain2">
            <div class="contain3">
                <img class="icon1" :src="iconMap[link]"/>
                <img class="icon1" :src="iconMap[can]">
                <img class="icon1" :src="iconMap['codeicon']"/>
                <span class="iplabel">{{addr}}</span> 
                <img class="icon2" :src="iconMap['nameicon']"/>
                <span class="maclabel">{{name}}</span>
            </div>
            <div class="contain4">
                <img class="icon3" :src="iconMap['infoicon']"/>
                <span class="maclabel">{{value}}</span>
                <img class="icon4" :src="iconMap['time']"/>
                <span class="maclabel">{{time}}</span>
            </div>
        </div>
      </div>
    <mpvue-echarts :echarts="echarts" :onInit="onInit" canvasId="demo-canvas"/>
  </div>
</template>

<script>
import mpvueEcharts from 'mpvue-echarts';
import echarts from '../../../static/echarts.min';
/* eslint-disable  */
  const iconMap = {
    烟雾: '/static/images/common/smog.png',
    开停: '/static/images/common/switch.png',
    温湿度: '/static/images/common/tempth.png',
    负压: '/static/images/common/vol.png',
    断电器: '/static/images/common/breaker.png',
    激光甲烷: '/static/images/common/line.png',
    甲烷高低浓: '/static/images/common/line.png',
    二氧化碳: '/static/images/common/co2.png',
    风速: '/static/images/common/speed.png',
    读卡器: '/static/images/common/reader.png',
    甲烷低浓度: '/static/images/common/ch4.png',
    广播终端: '/static/images/common/boardcast.png',
    氧气: '/static/images/common/o2.png',
    一氧化碳: '/static/images/common/co.png',
    粉尘: '/static/images/common/drug.png',
    语音风门: '/static/images/common/fengmen.png',
    转换器: '/static/images/common/switcher.png',
    风筒: '/static/images/common/fengtong.png',
    电源: '/static/images/common/power.png',
    CAN1: '/static/images/common/index1.png',
    CAN2: '/static/images/common/index2.png',
    CAN3: '/static/images/common/index3.png',
    CAN4: '/static/images/common/index4.png',
    CANNONE: '/static/images/common/none.png',
    正常: '/static/images/common/link.png',
    中断: '/static/images/common/linkbreak.png',
    未知: '/static/images/common/linkbreak.png',
    等待连接: '/static/images/common/waitlink.png',
    综合分站: '/static/images/common/station.png',
    ipicon: '/static/images/common/ipicon.png',
    codeicon: '/static/images/common/code.png',
    nameicon: '/static/images/common/name.png',
    infoicon: '/static/images/common/infoicon.png',
    positionicon: '/static/images/common/position.png',
    time: '/static/images/common/time.png',
  };
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
      iconMap,
      onInit: initChart,
      ipandaddr: null,
      freshaddr: null,
      link: '中断',
      name: '风速',
      can: 'CANNONE',
      value: '',
      addr: '',
      fresh: null,
      ip: '',
      time: '',
    };
  },
  onLoad(options) {
    this.ipandaddr = options.ipandaddr;
    this.addr = options.addr;
    this.name = options.name;
    this.fresh = '1';
    this.ip = options.ip;
    wx.setNavigationBarTitle({
      title: '传感器历史数据'
    });
  },
  created() {
    wx.showLoading();
    setInterval(() => {
              if ((this.ipandaddr != null) && (this.fresh != null)) {
                 wx.request({
                url: `https://api.zouyang.ltd/sensorcurinfo?ipandaddr=${this.ipandaddr}&ip=${this.ip}`,
                success: (res) => {
                    this.name = res.data.name;
                    this.link = res.data.link;
                    this.can = res.data.can;
                    this.value = res.data.value;
                    this.addr = res.data.addr;
                    this.time = res.data.time;
                  },
                });
              }
            },1000);
    setInterval(() => {
      if (chart != null) {
        if ((this.ipandaddr != null) && (this.fresh != null)) {
          if (this.ipandaddr != this.freshaddr) {
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
                  backgroundColor:'',//背景颜色透明
                  label: {
                    nomal: {
                      show: true
                    }
                  },
                  calculable: true,
                  tooltip: {
                    trigger: 'axis',
                    formatter: `采集时间：{b0}\n监测值：{c0}${res.data[0].unit}`,
                    backgroundColor: 'rgba(72, 209, 204, 0.6)',
                    borderWidth: 3,
                    borderColor: '#ccc',
                    textStyle: {
                        color: '#FFF'
                    },
                    axisPointer:{
                      show: true,
                      type : 'cross',
                      lineStyle: {
                        type : 'solid',
                        width : 2
                      },
                      label:{
                        show:false
                      }
                    },
                    position: function(point, params, dom, rect, size){
                    if(point[0]>(size.viewSize[0]/2))
                      return [point[0]-135,point[1]-100];
                    else
                      return [point[0]+20,point[1]-100];
                    }
                },
                  dataZoom: [{
                    type: "slider",
                    realtime: true,
                    show: true,
                    start: 0,
                    end: 100,
                    handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
                    handleSize: '100%',
                    handleStyle: 
                    {
                      color: '#fff',
                      shadowBlur: 3,
                      shadowColor: 'rgba(0, 0, 0, 0.6)',
                      shadowOffsetX: 2,
                      shadowOffsetY: 2
                    }
                  }],
                  xAxis: {
                    type: "category",
                    boundaryGap: true,
                    data: listx,
                    show: true
                  },
                  yAxis: {
                    x: "center",
                    type: "value",
                    show: true,
                  },
                  series: [
                    {
                      name: "监测值",
                      type: "line",
                      data: listy,
                      smooth: true,
                      showAllSymbol: false,
                      itemStyle: {  
                      normal: {   //颜色渐变函数 前四个参数分别表示四个位置依次为左、下、右、上
                            color: new echarts.graphic.LinearGradient(0, 0, 0, 1,[{
                                    offset: 0, color: '#d7f4f8' // 0% 处的颜色
                                }, {
                                    offset: 0.3, color: '#eefcfd' // 100% 处的颜色
                                }, {
                                    offset: 1, color: '#fff' // 100% 处的颜色
                                }]
                            ),  //背景渐变色 
                      lineStyle: {        // 系列级个性化折线样式  
                                width: 2,  
                                type: 'solid',  
                                color: "#4fd6d2"
                            }
                          },  
                      emphasis: {  
                          color: '#4fd6d2',  
                          lineStyle: {        // 系列级个性化折线样式  
                          width:2,  
                          type: 'dotted',  
                          color: "#4fd6d2" //折线的颜色
                    }  
                }  
                },//线条样式  
                  symbolSize:0.001, //折线点的大小
                  areaStyle: {normal: {}},
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
    }, 100);
  },
  onUnload() {
    this.ipandaddr = null;
    this.freshaddr = null;
    this.fresh = null;
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
                  backgroundColor:'',//背景颜色透明
                  label: {
                    nomal: {
                      show: true
                    }
                  },
                  calculable: false,
                  tooltip: {
                    trigger: 'axis',
                    formatter: `采集时间：{b0}\n监测值：{c0}${res.data[0].unit}`,
                    // backgroundColor: '#eefcfd',
                    borderWidth: 3,
                    borderColor: '#ccc',
                    padding: 10,
                    textStyle: {
                        color: '#FFF'
                    },
                    position: function(point, params, dom, rect, size){
                    if(point[0]>(size.viewSize[0]/2))
                      return [point[0]-135,point[1]-100];
                    else
                      return [point[0]+20,point[1]-100];
                    }
                },
                  dataZoom: [{
                    type: "slider",
                    realtime: true,
                    show: true,
                    start: 0,
                    end: 100,
                    handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
                    handleSize: '100%',
                    handleStyle: 
                    {
                      color: '#fff',
                      shadowBlur: 3,
                      shadowColor: 'rgba(0, 0, 0, 0.6)',
                      shadowOffsetX: 2,
                      shadowOffsetY: 2
                    }
                  }],
                  xAxis: {
                    type: "category",
                    boundaryGap: true,
                    data: listx,
                    show: true
                  },
                  yAxis: {
                    x: "center",
                    type: "value",
                    show: true,
                  },
                  series: [
                    {
                      name: "监测值",
                      type: "line",
                      data: listy,
                      smooth: true,
                      showAllSymbol: false,
                      itemStyle: {  
                      normal: {   //颜色渐变函数 前四个参数分别表示四个位置依次为左、下、右、上
                            color: new echarts.graphic.LinearGradient(0, 0, 0, 1,[{
                                    offset: 0, color: '#d7f4f8' // 0% 处的颜色
                                }, {
                                    offset: 0.3, color: '#eefcfd' // 100% 处的颜色
                                }, {
                                    offset: 1, color: '#fff' // 100% 处的颜色
                                }]
                            ),  //背景渐变色 
                      lineStyle: {        // 系列级个性化折线样式  
                                width: 2,  
                                type: 'solid',  
                                color: "#4fd6d2"
                            }
                          },  
                      emphasis: {  
                          color: '#4fd6d2',  
                          lineStyle: {        // 系列级个性化折线样式  
                          width:2,  
                          type: 'dotted',  
                          color: "#4fd6d2" //折线的颜色
                    }  
                }  
                },//线条样式  
                  symbolSize:0.001, //折线点的大小
                  areaStyle: {normal: {}},
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
  box-shadow: 0 0 25rpx rgb(79, 189, 143) inset;
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
  font-size: 24rpx;
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
.icon4{
  width: 32rpx;
  height: 32rpx;
  margin-left: 50rpx;
}
.echarts-wrap {
  width: 100%;
  height: 600rpx;
}
</style>
