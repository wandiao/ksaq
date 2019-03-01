<template>
  <div>
    <i-sticky :scrollTop="scrollTop">
        <i-sticky-item i-class="i-sticky-demo-title">
          <view slot="title">
              <i-col span="3"><div class="contain1">
                <img class="icon1" :src="iconMap['综合分站']" />
                </div>
                </i-col>
              <i-col span="21">
                <i-tag class="i-tags" name="1" color="yellow" >{{position}}</i-tag>
                <i-tag class="i-tags" name="1" color="green" >{{ip}}</i-tag>
                <i-tag class="i-tags" name="1" color="blue" >{{version}}</i-tag>
                <i-tag class="i-tags" name="1" color="green" >{{time}}</i-tag>
              </i-col>
          </view>
          <view slot="content" v-for="(item, index) in list" :key="index" @click="showchart(item.ipandaddr,item.addr,item.name,ip)">
            <div class="contain">
            <i-row>
              <i-col span="4"><img class="icon" :src="iconMap[item.name]"/></i-col>
              <i-col span="20">
                <i-row> <i-tag class="i-tags" name="1" color="yellow">{{item.addr}}#{{item.name}}</i-tag></i-row>
                <i-row> <i-tag class="i-tags" name="1" :color= iconMap[item.link] >{{item.link}}</i-tag>
                <i-tag class="i-tags" name="1" :color= iconMap[item.can] >{{item.can}}</i-tag>
                <i-tag class="i-tags" name="1" color="green" >{{item.value}}</i-tag></i-row>
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
    CAN1: 'blue',
    CAN2: 'blue',
    CAN3: 'blue',
    CAN4: 'blue',
    CANNONE: 'red',
    正常: 'green',
    中断: 'red',
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
      showchart(ipandaddr, addr, name, ip) {
        this.fresh = null;
        wx.navigateTo({
          url: `/pages/sensorchart/main?ipandaddr=${ipandaddr}&addr=${addr}&name=${name}&ip=${ip}`,
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
      this.fresh = null;
      this.scrollTop = 0;
    },
  };
</script>
<style lang="less" scoped>
  @import '../../styles/mixin';
.icon{
  width: 105rpx;
  height: 105rpx;
  box-shadow:0 0 15px rgb(15, 112, 141) inset,0 0 5px rgb(15, 112, 141);
}
.icon1{
  width: 48rpx;
  height: 48rpx;
  box-shadow:0 0 15px rgb(15, 112, 141) inset,0 0 5px rgb(15, 112, 141);
  margin-top: 5rpx;
}
.contain{
  margin-top: 15rpx;
  box-shadow:0 0 5px rgb(50, 52, 53);
}
.contain1{
  margin-top: 5rpx;
}
</style>
