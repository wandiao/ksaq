<template>
  <div class="home container">
    <div plain="true" size="mini" class="fl">
       <img class="icon4" :src="iconMap['ipicon']"/>
       <span class="titlelabel">{{ip}}</span>
       <img class="icon4" :src="iconMap['time']"/>
       <span class="titlelabel">{{time}}</span>
    </div>
    <ul class="tb">
      <div plain="true" size="mini" v-for="(item, index) in list" :key="index" @click="showchart(item.ipandaddr,item.addr,item.name,ip)" class="tr">
        <div class="contain1" ><img class="icon" :src="iconMap[item.name]" float:left /></div>
        <div class="contain2">
            <div class="contain3">
                <img class="icon1" :src="iconMap[item.link]"/>
                <img class="icon1" :src="iconMap[item.can]">
                <img class="icon1" :src="iconMap['codeicon']"/>
                <span class="iplabel">{{item.addr}}</span> 
                <img class="icon2" :src="iconMap['nameicon']"/>
                <span class="maclabel">{{item.name}}</span>
            </div>
            <div class="contain4">
                <img class="icon3" :src="iconMap['infoicon']"/>
                <span class="maclabel">{{item.value}}</span>
            </div>
        </div>
      </div>
    </ul>
  </div>
</template>

<script>
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

  export default {
    data() {
      return {
        list: [],
        time: '--:--:--',
        iconMap,
        ip: null,
        fresh: null,
      };
    },
    methods: {
      showchart(ipandaddr, addr, name, ip) {
        this.fresh = null;
        wx.navigateTo({
          url: `/pages/sensorchart/main?ipandaddr=${ipandaddr}&addr=${addr}&name=${name}&ip=${ip}`,
        });
      },
    },
    onLoad(options) {
      this.fresh = '1';
      this.ip = options.ip;
      wx.setNavigationBarTitle({
        title: '分站实时数据',
      });
    },
    onPullDownRefresh() {
      wx.showLoading();
      wx.request({
        url: `https://api.zouyang.ltd/curinfo?ip=${this.ip}`,
        success: (res) => {
          wx.hideLoading();
          this.list = res.data;
          wx.stopPullDownRefresh();
        },
      });
    },
    created() {
      wx.showLoading();
      setInterval(() => {
        if (this.ip != null) {
          if (this.fresh != null) {
            wx.request({
              url: `https://api.zouyang.ltd/curinfo?ip=${this.ip}`,
              success: (res) => {
                wx.hideLoading();
                this.list = res.data;
                this.time = this.list[0].time;
              },
            });
          }
        }
      }, 1000);
    },
    onShow() {
      this.fresh = '1';
    },
    onUnload() {
      this.fresh = null;
    },
  };
</script>
<style lang="less" scoped>
  @import '../../styles/mixin';
.tr{
      display: flex;
      align-items: left;     /*垂直居中*/
      box-shadow:0 0 5rpx rgb(57, 58, 59);
      margin-top: 10rpx;
      margin-left: 10rpx;
      margin-right: 10rpx;
  }
.tb{
  margin-top: 106rpx;
  }
.fl{
  display: flex;
  top:  0;
  box-shadow:0 0 10rpx rgb(50, 53, 52);
  position: fixed;
  background-color: rgb(11, 192, 177);
  width: 100vw;
  height: 96rpx;
}
.contain3{
  display: flex;
  align-items: center;     /*垂直居中*/
  font-size: 30rpx;
  margin-top: 10rpx;
}
.contains4{
  font-size: 30rpx;
  margin-top: 10rpx;
  margin-bottom: 5rpx;
}
.icon{
  width: 96rpx;
  height: 96rpx;
  box-shadow:0 0 15px rgb(15, 112, 141) inset,0 0 5px rgb(15, 112, 141);
  margin-top: 5rpx;
  margin-bottom: 5rpx;
  margin-left: 5rpx;
}
.icon1{
  width: 32rpx;
  height: 32rpx;
  margin-left: 5rpx;
}
.icon2{
  width: 32rpx;
  height: 32rpx;
  margin-top: 5rpx;
  margin-left: 35rpx;
}
.icon3{
  width: 32rpx;
  height: 32rpx;
  margin-left: 5rpx;
}
.icon4{
  width: 64rpx;
  height: 64rpx;
  margin-top: 15rpx;
  margin-left: 50rpx;
}
.titlelabel{
  display: flex;
  align-items: center;     /*垂直居中*/
  font-size: 36rpx;
}
.tb{
    .button-hover {
  background-color: rgba(10, 88, 85, 0.603);
}
  .other-button-hover {
  background-color: blue;
}
}
  .tr {
    display: flex;
    .th, .td {
      position: relative;
      flex: 1;
      text-align: center;
      &:after {
        .hairline();
        border-bottom-width: 1rpx;
      }
    }
    .f0{
      flex: 1;
    }
    
    .th {
      color: #4e76e2cb;
    }
    .td {
      &.warn {
        color: #F05E4B;
      }
    }
  }
</style>
