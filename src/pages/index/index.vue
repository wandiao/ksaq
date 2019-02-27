<template>
  <div class="home container">
    <!-- <ul class="tb">
      <div plain="true" size="mini" v-for="(item, index) in list" :key="index" @click="jump(item.ip)" class="tr">
        <div class="contain1" ><img class="icon" :src="iconMap['综合分站']" float:left /></div>
        <div class="contain2">
            <div class="contain3">
                <img class="icon1" :src="iconMap['ipicon']"/>
                <span class="iplabel">{{item.ip}}</span> 
                <img class="icon2" :src="iconMap['macicon']"/>
                <span class="maclabel">{{item.mac}}</span>
            </div>
            <div class="contain4">
                <img class="icon1" :src="iconMap['verbicon']"/>
                <span class="iplabel" >{{item.version}}</span> 
                <img class="icon3" :src="iconMap['positionicon']"/>
                <span class="maclabel">{{item.position}}</span>
            </div>
        </div>
      </div>
    </ul> -->
    <van-row v-for="(item, index) in list" :key="index" @click="jump(item.ip)">
      <van-col><img class="icon" :src="iconMap['综合分站']" float:left /></van-col>
      <van-col>
        <van-row>
          <van-col><img class="icon1" :src="iconMap['ipicon']"/></van-col><van-col>{{item.ip}}</van-col> <van-col><img class="icon1" :src="iconMap['macicon']"/></van-col><van-col>{{item.mac}}</van-col>
        </van-row>
        <van-row>
          <van-col><img class="icon1" :src="iconMap['verbicon']"/></van-col>
          <van-col>{{item.version}}</van-col>
          <van-col><img class="icon1" :src="iconMap['positionicon']"/></van-col>
          <van-col>{{item.position}}</van-col>
        </van-row>
      </van-col>
    </van-row>
  </div>
</template>

<script>
  const iconMap = {
    烟雾: '/static/images/common/smog.png',
    开停: '/static/images/common/switch.png',
    温湿度: '/static/images/common/warm.png',
    负压: '/static/images/common/vol.png',
    断电馈电器: '/static/images/common/break.png',
    激光甲烷: '/static/images/common/line.png',
    甲烷高低浓: '/static/images/common/line.png',
    二氧化碳: '/static/images/common/co2.png',
    风速: '/static/images/common/speed.png',
    读卡器: '/static/images/common/card.png',
    甲烷低浓度: '/static/images/common/ch4.png',
    广播终端: '/static/images/common/boardcast.png',
    氧气: '/static/images/common/o2.png',
    一氧化碳: '/static/images/common/co.png',
    粉尘: '/static/images/common/drug.png',
    语音风门: '/static/images/common/fengmen.png',
    风筒: '/static/images/common/fengtong.png',
    电源: '/static/images/common/power.png',
    C1: '/static/images/common/index1.png',
    C2: '/static/images/common/index2.png',
    C3: '/static/images/common/index3.png',
    C4: '/static/images/common/index4.png',
    连接正常: '/static/images/common/link.png',
    连接断开: '/static/images/common/linkbreak.png',
    等待连接: '/static/images/common/waitlink.png',
    综合分站: '/static/images/common/station.png',
    ipicon: '/static/images/common/ipicon.png',
    macicon: '/static/images/common/macicon.png',
    verbicon: '/static/images/common/verbicon.png',
    positionicon: '/static/images/common/position.png',
  };

  export default {
    data() {
      return {
        list: [],
        iconMap,
      };
    },
    methods: {
      jump(ip) {
        wx.navigateTo({
          url: `/pages/sensorinfo/main?ip=${ip}`,
        });
      },
    },
    onLoad() {
      wx.setNavigationBarTitle({
        title: '分站列表',
      });
    },
    onPullDownRefresh() {
      wx.showLoading();
      wx.request({
        url: 'https://api.zouyang.ltd/stationlist',
        success: (res) => {
          console.log(res);
          wx.hideLoading();
          this.list = res.data;
          wx.stopPullDownRefresh();
        },
      });
    },
    created() {
      wx.showLoading();
      wx.request({
        url: 'https://api.zouyang.ltd/stationlist',
        success: (res) => {
          console.log(res);
          wx.hideLoading();
          this.list = res.data;
        },
      });
    },
  };
</script>

<style lang="less" scoped>
  @import '../../styles/mixin';
  .tr{
      display: flex;
      align-items: left;     /*垂直居中*/
      box-shadow:0 0 10rpx rgb(62, 63, 63);
      margin-top: 10rpx;
      margin-left: 10rpx;
      margin-right: 10rpx;
  }
.contain3{
  display: flex;
  align-items: center;     /*垂直居中*/
  font-size: 30rpx;
  margin-top: 15rpx;
  margin-bottom: 5rpx;
}
.contain4{
  display: flex;
  align-items: center;     /*垂直居中*/
  font-size: 30rpx;
  margin-top: 25rpx;
}
.icon{
  width: 128rpx;
  height: 128rpx;
  box-shadow:0 0 20px rgb(26, 99, 141) inset,0 0 5px rgb(9, 70, 52);
  margin-top: 5rpx;
  margin-bottom: 5rpx;
  margin-left: 5rpx;
}
.icon1{
  width: 32rpx;
  height: 32rpx;
}
.icon2{
  width: 32rpx;
  height: 32rpx;
  margin-left: 25rpx;
}
.icon3{
  width: 32rpx;
  height: 32rpx;
  margin-left: 45rpx;
}

.home {
    padding: 5rpx;
  }
.tb{
    .button-hover {
  background-color: rgba(0, 255, 242, 0.329);
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
