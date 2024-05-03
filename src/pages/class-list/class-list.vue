<template>
  <view class="classlist">
    
    <view class="goBack" @click="goBack" :style="{top:getStatusBarHeight()+'px'}">
      <uni-icons type="back" color="#fff" size="20"></uni-icons>
    </view>
    
    <view class="content">
      <navigator url="/pages/preview/preview" class="item" v-for="item in 10">
        <image src="@/common/images/preview2.jpg" mode="aspectFill"></image>
      </navigator>
    </view>
  </view>
</template>

<script setup>

import {getStatusBarHeight} from "@/utils/system";

const goBack = () => {
  uni.navigateBack({
    success: () => {
    
    },
    fail: (err) => {
      uni.reLaunch({
        url:"/pages/index/index"
      })
    }
  })
}

</script>

<style lang="scss" scoped>
.classlist {
  position: relative;
  .content {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 5rpx;
    padding: 5rpx;
    .item {
      height: 440rpx;
      image {
        width: 100%;
        height: 100%;
        display: block;
      }
    }
  }
  .goBack {
    position: absolute;
    width: 38px;
    height: 38px;
    background: rgba(0, 0, 0, 0.5);
    left: 30rpx;
    margin-left: 0;
    border-radius: 100px;
    top: 0;
    backdrop-filter: blur(10rpx);
    border: 1rpx solid rgba(255, 255, 255, 0.3);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
  }
}
</style>