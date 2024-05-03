<template>
  <view class="preview">
    <swiper circular>
      <swiper-item v-for="item in 5">
        <image src="@/common/images/preview2.jpg" mode="aspectFill" @click="maskChange"></image>
      </swiper-item>
    </swiper>
    
    <view class="mask" v-show="maskVisible">
      <view class="count">7 / 12</view>
      <view class="time">
        <uni-dateformat :date="new Date()" format="hh:mm"></uni-dateformat>
      </view>
      <view class="date">
        <uni-dateformat :date="new Date()" format="MM月dd日"></uni-dateformat>
      </view>
      <view class="footer">
        <view class="box" @click="popupinfo">
          <uni-icons type="info" size="28"></uni-icons>
          <view class="text">信息</view>
        </view>
        <view class="box" @click="popupStar">
          <uni-icons type="star" size="28"></uni-icons>
          <view class="text">5分</view>
        </view>
        <view class="box">
          <uni-icons type="download" size="28"></uni-icons>
          <view class="text">下载</view>
        </view>
      </view>
    </view>
    
    <uni-popup class="popupstar" ref="popupStarRef">
      <view class="starcontainer">
        <view class="starheader">
          <view></view>
          <view class="text">壁纸评分</view>
          <view class="close" @click="closeStar">
            <uni-icons type="closeempty" size="15" color="#999"></uni-icons>
          </view>
        </view>
        <view class="starcontent">
          <uni-rate touchable allow-half :value="3.5" size="35"/>
          <text class="score">5 分</text>
        </view>
        <view class="confirmstar">
          <button type="default" size="mini" plain>确认评分</button>
        </view>
      </view>
    </uni-popup>
    
    <uni-popup class="popup" ref="popupInfo">
      <view class="infoPopup">
        <view class="header">
          <view></view>
          <view class="title">壁纸信息</view>
          <view class="close" @click="clickInfoClose">
            <uni-icons type="closeempty" size="15" color="#999"></uni-icons>
          </view>
        </view>
        
        <scroll-view scroll-x>
          <view class="content">
            <view class="row">
              <view class="label">壁纸ID：</view>
              <text selectable class="value">作者信息的房间大师傅艰苦的肌肤哈桑地方还是大红</text>
            </view>
            <view class="row">
              <view class="label">发布者：</view>
              <text selectable class="value">作者信息</text>
            </view>
            <view class="row">
              <view class="label">评分：</view>
              <view class='value roteBox'>
                <uni-rate touchable allow-half :value="3.5" size="16"/>
                <text class="score">5 分</text>
              </view>
            </view>
            <view class="row">
              <view class="label">摘要：</view>
              <text selectable class="value">作者信息的健康沙发几点开始发售的和飞机喀什的话</text>
            </view>
            <view class="row">
              <view class="label">标签：</view>
              <view class='value tabs'>
                <view class="tab">
                  张嘉译
                </view>
              </view>
            </view>
          </view>
        </scroll-view>
      </view>
    </uni-popup>
  </view>
</template>

<script setup lang="ts">

import {ref} from "vue";

const maskVisible = ref(true)
const popupInfo = ref(null)
const popupStarRef = ref(null)

const closeStar = () => {
  popupStarRef.value.close()
}

const popupStar = () => {
  popupStarRef.value.open('center')
}

const clickInfoClose = () => {
  popupInfo.value.close()
}

const popupinfo = () => {
  popupInfo.value.open('bottom')
}

const maskChange = () => {
  maskVisible.value = !maskVisible.value
}

</script>

<style scoped lang="scss">
.preview {
  width: 100vw;
  height: 100vh;
  position: relative;
  
  swiper {
    width: 100%;
    height: 100%;
    
    image {
      width: 100%;
      height: 100%;
    }
  }
  
  .starcontainer {
    box-sizing: border-box;
    background: #fff;
    width: 520rpx;
    height: 340rpx;
    border-radius: 40rpx;
    padding: 40rpx;
    //display: flex;
    //flex-direction: column;
    //align-items: center;
    .starheader {
      display: flex;
      justify-content: space-between;
      color: $text-font-color-2;
    }
    .starcontent {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%;
      margin: auto;
      padding-top: 40rpx;
      .score {
        color: #FFCA3E;
        padding-left: 10rpx;
        width: 80rpx;
        line-height: 1em;
        text-align: right;
        font-size: 28rpx;
      }
    }
    
    .confirmstar {
      padding: 30rpx 0;
      display: flex;
      justify-content: center;
      align-items: center;
    }
  }
  
  .mask {
    & > view {
      position: absolute;
      left: 0;
      right: 0;
      margin: auto;
      width: fit-content;
    }
    
    .count {
      color: #fff;
      top: 10vh;
      background: rgba(0, 0, 0, 0.3);
      padding: 8rpx 28rpx;
      border-radius: 40rpx;
      backdrop-filter: blur(10rpx);
    }
  }
  
  .infoPopup {
    background: white;
    border-top-right-radius: 25rpx;
    border-top-left-radius: 25rpx;
    overflow: hidden;
    
    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 20rpx 25rpx;
      
      .title {
        font-size: 26rpx;
        color: $text-font-color-2;
      }
    }
    
    scroll-view {
      max-height: 60vh;
      
      .content {
        
        padding: 20rpx 25rpx;
        
        .row {
          display: flex;
          padding: 16rpx 0;
          font-size: 32rpx;
          line-height: 1.7em;
          
          .label {
            color: $text-font-color-2;
            width: 140rpx;
            text-align: right;
            font-size: 30rpx;
          }
          
          .value {
            flex: 1;
            font-weight: 550;
            
          }
          
          .roteBox {
            display: flex;
            align-items: center;
            
            .score {
              font-size: 26rpx;
              color: $score-color;
              padding-left: 10rpx;
            }
          }
          
          .tabs {
            display: flex;
            flex-wrap: wrap;
            
            .tab {
              border: 1px solid $brand-theme-color;
              color: $brand-theme-color;
              font-size: 22rpx;
              padding: 10rpx 30rpx;
              border-radius: 40rpx;
              line-height: 1em;
              margin: 0 10rpx 10rpx 0;
            }
          }
        }
      }
    }
    
  }
  
  .time {
    color: #fff;
    font-size: 140rpx;
    top: calc(10vh + 80rpx);
    font-weight: 100;
    line-height: 1em;
    text-shadow: 0 4rpx rgba(0, 0, 0, 0.3);
  }
  
  .date {
    color: #fff;
    font-size: 35rpx;
    top: calc(20vh + 90rpx);
  }
  
  .footer {
    bottom: 10vh;
    display: flex;
    justify-content: space-around;
    align-items: center;
    background: rgba(255, 255, 255, 0.8);
    width: 65vw !important;
    height: 120rpx;
    border-radius: 120rpx;
    box-shadow: 0 2rpx 0 rgba(0, 0, 0, 0.1);
    backdrop-filter: blur(20rpx);
    
    .box {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 2rpx 12rpx;
      
      .text {
        font-size: 26rpx;
        color: $text-font-color-2;
      }
    }
  }
}
</style>
