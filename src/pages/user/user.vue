<template>
  <view class="userLayout pageBG" v-if="userinfo">
    <view :style="{height:getNavBarHeight()+'px'}"></view>
    <view class="userInfo">
      <view class="pic">
        <image src="../../static/xxmLogo.png" mode="aspectFill"></image>
      </view>
      <view class="ip">{{ userinfo.IP }}</view>
      <view class="address">来自于：
        {{ userinfo.address.city || userinfo.address.province || userinfo.address.country }}
      
      </view>
    </view>
    
    
    <view class="section">
      <view class="list">
        <navigator url="/pages/class-list/class-list?name=我的下载&type=download" class="row">
          <view class="left">
            <uni-icons type="download-filled" size="20"></uni-icons>
            <view class="text">我的下载</view>
          </view>
          <view class="right">
            <view class="text">{{userinfo.downloadSize}}</view>
            <uni-icons type="right" size="15"></uni-icons>
          </view>
        </navigator>
        <navigator url="/pages/class-list/class-list?name=我的评分&type=score" class="row">
          <view class="left">
            <uni-icons type="star-filled" size="20"></uni-icons>
            <view class="text">我的评分</view>
          </view>
          <view class="right">
            <view class="text">{{userinfo.scoreSize}}</view>
            <uni-icons type="right" size="15"></uni-icons>
          </view>
        </navigator>
        <view class="row">
          <view class="left">
            <uni-icons type="weixin" size="20"></uni-icons>
            <view class="text">联系客服</view>
          </view>
          <view class="right">
            <view class="text"></view>
            <uni-icons type="right" size="15"></uni-icons>
          </view>
          <!-- #ifdef MP -->
          <button open-type="contact">联系客服</button>
          <!-- #endif -->
          <!-- #ifndef MP -->
          <button @click="onTele">拨打电话</button>
          <!-- #endif -->
        </view>
      </view>
    </view>
    
    <view class="section">
      <view class="list">
        <navigator class="row">
          <view class="left">
            <uni-icons type="notification-filled" size="20"></uni-icons>
            <view class="text">订阅更新</view>
          </view>
          <view class="right">
            <view class="text"></view>
            <uni-icons type="right" size="15"></uni-icons>
          </view>
        </navigator>
        <navigator class="row">
          <view class="left">
            <uni-icons type="flag-filled" size="20"></uni-icons>
            <view class="text">常见问题</view>
          </view>
          <view class="right">
            <view class="text"></view>
            <uni-icons type="right" size="15"></uni-icons>
          </view>
        </navigator>
      </view>
    </view>
  </view>
  <view class="loadingLayout" v-else>
    <view :style="{height:getNavBarHeight()+'px'}"></view>
    <uni-load-more status="loading"></uni-load-more>
  </view>
</template>

<script setup>

import {apiUserInfo} from "@/api/apis";
import {ref} from "vue";
import {getNavBarHeight} from "@/utils/system";

const userinfo = ref(null)

const getUserInfo = () => {
  apiUserInfo().then(res => {
    console.log(res);
    userinfo.value = res.data
  })
}

const onTele = () => {
  uni.makePhoneCall({
    phoneNumber: '19334023967',
    complete: () => {
    }
  })
}

getUserInfo();
</script>

<style lang="scss" scoped>
.userLayout {
  .userInfo {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    padding: 50rpx 0;
    
    .pic {
      width: 160rpx;
      height: 160rpx;
      border-radius: 50%;
      overflow: hidden;
      
      image {
        width: 100%;
        height: 100%;
      }
    }
    
    .ip {
      font-size: 44rpx;
      color: #333;
      padding: 20rpx 0 5rpx;
    }
    
    .address {
      font-size: 28rpx;
      color: #aaa;
    }
  }
  
  .section {
    width: 690rpx;
    margin: 50rpx auto;
    border: 1px solid #eee;
    border-radius: 10rpx;
    box-shadow: 0 0 30rpx rgba(0, 0, 0, 0.05);
    
    .list {
      .row {
        display: flex;
        justify-content: space-between;
        padding: 0 30rpx;
        align-items: center;
        height: 100rpx;
        border-bottom: 1px solid #eee;
        position: relative;
        background-color: white;
        
        &:last-child {
          border-bottom: none;
        }
        
        .left {
          display: flex;
          align-items: center;
          
          :deep() {
            .uni-icons {
              color: $brand-theme-color !important;
            }
          }
          
          .text {
            padding-left: 20rpx;
            color: #666
          }
        }
        
        .right {
          display: flex;
          align-items: center;
          
          .text {
            padding-right: 10rpx;
          }
        }
        
        button {
          position: absolute;
          top: 0;
          left: 0;
          height: 100rpx;
          width: 100%;
          opacity: 0;
        }
      }
    }
  }
}
</style>