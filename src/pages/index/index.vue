<template>
  <div class="home container">
    <div class="tr">
      <div class="th">Type</div>
      <div class="th f0">Addr</div>
      <div class="th f0">Link</div>
      <div class="th f0">Can</div>
      <div class="th">Value</div>
    </div>
    <ul class="tb">
      <li v-for="(item, index) in list" :key="index" class="tr">
        <div class="td"><img class="icon" :src="iconMap[item.SensorName]"/>{{item.SensorName}}</div>
        <div class="td f0">{{item.SensorAddr}}</div>
        <div class="td f0"><img class="icon" :src="iconMap[item.LinkState]"/></div>
        <div class="td f0"><img class="icon" :src="iconMap[item.CanCode]"/></div>
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
      }, 1000);
    },
  };
</script>

<style lang="less" scoped>
  @import '../../styles/mixin';
  .home {
    padding: 20rpx;
  }
  .tr {
    display: flex;
    
    .th, .td {
      position: relative;
      flex: 2;
      text-align: center;
      font-size: 70%;
      padding: 18rpx 0;
      padding-left: 5rpx;
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
