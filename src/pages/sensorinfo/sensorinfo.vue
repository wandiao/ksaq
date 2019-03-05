<template>
  <div>
    <i-sticky :scrollTop="scrollTop">
        <i-sticky-item i-class="i-sticky-demo-title">
          <view slot="title">
            <div class="title">
              <i-col span="24">
                <i-tag class="i-tags" name="1" color="yellow" type="border">{{position}}</i-tag>
                <i-tag class="i-tags" name="1" color="green" type="border">{{ip}}</i-tag>
                <i-tag class="i-tags" name="1" color="blue" type="border">{{version}}</i-tag>
                <i-tag class="i-tags" name="1" color="yellow" type="border">{{time}}</i-tag>
              </i-col>
            </div>
          </view>
          <view slot="content" v-for="(item, index) in list" :key="index" @click="showchart(item.ipandaddr,item.addr,item.name,ip,unit)">
            <div class="contain">
            <i-row>
              <i-col span="3"><img class="icon" :src="iconMap[item.name]"/></i-col>
              <i-col span="21">
                <i-col span = "12">
                  <i-row><div class="addr">{{item.addr}}#{{item.name}}</div></i-row>
                  <i-row> 
                      <img class="icon2" :src="iconMap[item.link]"/>
                      <img class="icon2" :src="iconMap[item.can]"/>
                  </i-row>
                </i-col>
                <i-col span="12"><div class="value">{{item.value}}</div></i-col>
              </i-col>
            </i-row>
            </div>
          </view>
        </i-sticky-item>
         </i-sticky>
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
        position: '',
        version: '',
        iconMap,
        ip: null,
        fresh: null,
        scrollTop: 0,
      };
    },
    methods: {
      showchart(ipandaddr, addr, name, ip, unit) {
        this.fresh = null;
        wx.navigateTo({
          url: `/pages/sensorchart/main?ipandaddr=${ipandaddr}&addr=${addr}&name=${name}&ip=${ip}&unit=${unit}`,
        });
      },
      getcolor(can) {
        return can === 'CANNONE' ? 'red' : 'blue';
      },
    },
    onLoad(options) {
      this.fresh = '1';
      this.ip = options.ip;
      this.position = options.position;
      this.version = options.version;
      wx.setNavigationBarTitle({
        title: '分站实时数据',
      });
    },
    onPageScroll() {
      this.scrollTop = 1;
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
      this.list = [];
      this.fresh = null;
      this.scrollTop = 0;
    },
  };
</script>
<style lang="less" scoped>
.icon{
  width: 90rpx;
  height: 90rpx;
  box-shadow:0 0 5px rgb(53, 155, 185) inset;
  border-radius:10rpx;
}
.icon2{
  width: 36rpx;
  height: 36rpx;
}
.value{
  text-align:right; /*水平居中*/
  line-height: 96rpx; /*行距设为与div高度一致*/
  color: rgb(1, 143, 112);
}
.contain{
  margin-top: 8rpx;
  box-shadow:0 0 5px rgb(107, 111, 112);
  width: 97%;
  margin-left: 10rpx;
  border-radius:10rpx;
  height: 90rpx;
}
.contain1{
  margin-top: 5rpx;
}
.title{
  text-align:center; /*水平居中*/
}
.addr{
  text-align: left;
  font-size: 32rpx;
}
</style>
