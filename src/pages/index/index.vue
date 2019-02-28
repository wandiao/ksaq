<template>
  <div class="home container">
    <i-card full="true" :title=item.position :thumb="iconMap['综合分站']" v-for="(item, index) in list" :key="index" @click="jump(item.ip, item.position, item.version)">
      <view slot="content">
        <i-tag class="i-tags" name="1" color="blue" >{{item.ip}}</i-tag>
        <i-tag class="i-tags" name="1" color="green" >{{item.mac}}</i-tag>
        <i-tag class="i-tags" name="1" color="blue" >{{item.version}}</i-tag>
      </view>
    </i-card>      
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
      jump(ip, position, version) {
        wx.navigateTo({
          url: `/pages/sensorinfo/main?ip=${ip}&position=${position}&version=${version}`,
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
          wx.hideLoading();
          this.list = res.data;
        },
      });
    },
  };
</script>

<style lang="less" scoped>
  @import '../../styles/mixin';
</style>
