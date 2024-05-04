<template>
  <view class="preview" v-if="currentInfo">
    <swiper circular :current="currentIndex" @change="swiperChange">
      <swiper-item v-for="(item,index) in classList" :key="item._id">
        <image v-if="readImgs.includes(index)" :src="item.picurl" mode="aspectFill" @click="maskChange"></image>
      </swiper-item>
    </swiper>
    
    <view class="mask" v-show="maskVisible">
      
      <!-- #ifndef MP-TOUTIAO -->
      <view class="goBack" @click="goBack" :style="{top:getStatusBarHeight()+'px'}">
        <uni-icons type="back" color="#fff" size="20"></uni-icons>
      </view>
      <!-- #endif -->
      
      <view class="count">{{ currentIndex + 1 }} / {{ classList.length }}</view>
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
          <view class="text">{{ currentInfo.score }}分</view>
        </view>
        <view class="box">
          <uni-icons type="download" size="28"></uni-icons>
          <view class="text">下载</view>
        </view>
      </view>
    </view>
    
    <uni-popup class="popupstar" ref="popupStarRef" is-mask-click="false">
      <view class="starcontainer">
        <view class="starheader">
          <view></view>
          <view class="text">{{ isScore ? '评分过了~' : '壁纸评分' }}</view>
          <view class="close" @click="clickScoreClose">
            <uni-icons type="closeempty" size="15" color="#999"></uni-icons>
          </view>
        </view>
        <view class="starcontent">
          <uni-rate v-model="userScore" allowHalf :disabled="isScore" disabled-color="#FFCA3E"/>
          <text class="score">{{ userScore }}分</text>
        </view>
        <view class="confirmstar">
          <!--          还没评分或者已经评过了禁用-->
          <button @click="submitScore" :disabled="!userScore || isScore" type="default" size="mini" plain>
            确认评分
          </button>
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
              <text selectable class="value">{{ currentInfo._id }}</text>
            </view>
            <view class="row">
              <view class="label">发布者：</view>
              <text selectable class="value">{{ currentInfo.nickname }}</text>
            </view>
            <view class="row">
              <view class="label">评分：</view>
              <view class='value roteBox'>
                <uni-rate touchable allow-half :value="currentInfo.score" size="16" readonly/>
                <text class="score">{{ currentInfo.score }}分</text>
              </view>
            </view>
            <view class="row">
              <view class="label">摘要：</view>
              <text selectable class="value">{{ currentInfo.description }}</text>
            </view>
            <view class="row">
              <view class="label">标签：</view>
              <view class='value tabs'>
                <view class="tab" v-for="tab in currentInfo.tabs" :key="tab">
                  {{ tab }}
                </view>
              </view>
            </view>
            <view class="copyright">
              声明：本图片来用户投稿，非商业使用，用于免费学习交流，
              如侵犯了您的权益，您可以拷贝壁纸ID举报至平台，邮箱513894357@qq.com，
              管理将删除侵权壁纸，维护您的权益。
            </view>
          </view>
          <view class="safe-area-inset-bottom"></view>
        </scroll-view>
      </view>
    </uni-popup>
  </view>
</template>

<script setup lang="ts">

import {ref} from "vue";
import {getStatusBarHeight} from "@/utils/system";
import {onLoad} from "@dcloudio/uni-app";
import {apiDetailWall, apiGetSetupScore} from "@/api/apis";

const userScore = ref(0)
const isScore = ref(false)
const maskVisible = ref(true)
const popupInfo = ref(null)
const popupStarRef = ref(null)
const classList = ref([]);
const currentId = ref(null)
const currentIndex = ref(0)
const currentInfo = ref(null);
const readImgs = ref([]);

//将存储再localstorage得数据取出放入classList
const storgClassList = uni.getStorageSync("storgClassList") || [];
//将原数据做处理
classList.value = storgClassList.map(item => {
  return {
    ...item,
    picurl: item.smallPicurl.replace("_small.webp", ".jpg")
  }
})

//在一加载时做的事
onLoad(async (e) => {
  currentId.value = e.id;
  if (e.type == 'share') {
    let res = await apiDetailWall({id: currentId.value});
    classList.value = res.data.map(item => {
      return {
        ...item,
        picurl: item.smallPicurl.replace("_small.webp", ".jpg")
      }
    })
  }
  
  //从数据集里拿出当前数据
  currentIndex.value = classList.value.findIndex(item => item._id == currentId.value)
  currentInfo.value = classList.value[currentIndex.value]
  readImgsFun();
})

//评分 完之后将结果存入localstorage
const submitScore = async () => {
  uni.showLoading({
    title: "加载中..."
  })
  let {classid, _id: wallId} = currentInfo.value;
  let res = await apiGetSetupScore({
    classid,
    wallId,
    userScore: userScore.value
  })
  uni.hideLoading();
  if (res.errCode === 0) {
    uni.showToast({
      title: "评分成功",
      icon: "none"
    })
    classList.value[currentIndex.value].userScore = userScore.value;
    uni.setStorageSync("storgClassList", classList.value);
    clickScoreClose();
  }
}

//打开评分弹窗
const popupStar = () => {
  // 如果已经评过了 设置用户评过得分
  if (currentInfo.value.userScore) {
    isScore.value = true;
    userScore.value = currentInfo.value.userScore;
  }
  popupStarRef.value.open();
}

//关闭评分框
const clickScoreClose = () => {
  popupStarRef.value.close();
  userScore.value = 0;
  isScore.value = false;
}

const swiperChange = (e) => {
  currentIndex.value = e.detail.current;
  currentInfo.value = classList.value[currentIndex.value]
  readImgsFun();
  console.log(e);
}

function readImgsFun() {
  readImgs.value.push(
      currentIndex.value <= 0 ? classList.value.length - 1 : currentIndex.value - 1,
      currentIndex.value,
      currentIndex.value >= classList.value.length - 1 ? 0 : currentIndex.value + 1
  )
  readImgs.value = [...new Set(readImgs.value)];
}


const goBack = () => {
  uni.navigateBack({
    success: () => {
    
    },
    fail: (err) => {
      uni.reLaunch({
        url: "/pages/index/index"
      })
    }
  })
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
    
    .goBack {
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
        
        .copyright {
          font-size: 28rpx;
          padding: 20rpx;
          background: #F6F6F6;
          color: #666;
          border-radius: 10rpx;
          margin: 20rpx 0;
          line-height: 1.6em;
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
