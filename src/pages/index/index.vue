<template>
  <div class="home container">
    <div class="tr">
      <div class="th">设备类型</div>
      <div class="th">link</div>
      <div class="th">监控值</div>
    </div>
    <ul class="tb">
      <li v-for="(item, index) in list" :key="index" class="tr">
        <div class="td"><img class="icon" :src="iconMap[item.SensorName]"/>{{item.SensorName}}</div>
        <div class="td"><img class="icon" :src="iconMap[item.LinkState]"/></div>
        <div class="td">{{item.Value || '--'}}</div>
      </li>
    </ul>
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
    二氧化碳: '/static/images/common/co2.png',
    风速: '/static/images/common/speed.png',
    等待连接: '/static/images/common/wait.png',
    读卡器: '/static/images/common/card.png',
    甲烷低浓度: '/static/images/common/ch4.png',
    广播终端: '/static/images/common/boardcast.png',


    连接正常: '/static/images/common/link.png',
    连接断开: '/static/images/common/linkbreak.png',
  };

  export default {
    data() {
      return {
        list: [],
        iconMap,
      };
    },

    methods: {
      clickHandle(msg, ev) {
        console.log('clickHandle:', msg, ev);
      },
    },
    created() {
      wx.showLoading();
      wx.request({
        url: 'https://api.zouyang.ltd',
        success: (res) => {
          console.log(res);
          wx.hideLoading();
          this.list = res.data.sensorBeans;
        },
      });
      setInterval(() => {
        wx.request({
          url: 'https://api.zouyang.ltd',
          success: (res) => {
            this.list = res.data.sensorBeans;
          },
        });
      }, 2000);
    },
  };
</script>

<style lang="less" scoped>
  @import '../../styles/mixin';
  .home {
    padding: 30rpx;
  }
  .tr {
    display: flex;
    .th, .td {
      position: relative;
      flex: 1;
      // text-align: center;
      padding: 15rpx 0;
      padding-left: 25rpx;
      &:after {
        .hairline();
        border-bottom-width: 1rpx;
      }
    }
    .th {
      color: #58C8DC;
    }
    .td {
      &.warn {
        color: #F05E4B;
      }
    }
  }
</style>
