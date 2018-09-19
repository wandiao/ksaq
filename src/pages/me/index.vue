<template>
  <div class="me container">
    <div class="user-info" v-if="userInfo">
      <img class="avatar" :src="userInfo.avatarUrl" />
      <div class="u-name">{{userInfo.nickName}}</div>
    </div>
  </div>
</template>

<script>

export default {

  data() {
    return {
      userInfo: null,
    };
  },
  methods: {
    getUserInfo() {
      // 调用登录接口
      wx.login({
        success: () => {
          console.log(1);
          wx.getUserInfo({
            success: (res) => {
              this.userInfo = res.userInfo;
              console.log(this.userInfo);
            },
          });
        },
      });
    },
  },
  created() {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo();
  },
};
</script>

<style lang="less">
@import '../../styles/mixin';

.me {
  padding: 30rpx;
  font-size: 30rpx;
}
.user-info {
  .flex-column(center, center);
  width: 100vw;
  height: 100vh;
  img {
    width: 128rpx;
    height: 128rpx;
    margin: 20rpx;
    border-radius: 50%;
    display: block;
  }
}
</style>
