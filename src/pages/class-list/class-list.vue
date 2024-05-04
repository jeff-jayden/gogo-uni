<template>
  <view class="classlist">
    
    <view class="loading-layout" v-if="!classifyList.length && !noData">
      <uni-load-more status="loading"></uni-load-more>
    </view>
    
    <view class="content">
      <navigator
          :url="'/pages/preview/preview?id='+item._id"
          class="item"
          v-for="item in classifyList"
      >
        <image :src="item.smallPicurl" mode="aspectFill"></image>
      </navigator>
    </view>
  </view>
  
  <view class="loading-layout" v-if="classifyList.length || noData">
    <uni-load-more :status="noData ? 'noMore' : 'loading'"></uni-load-more>
  </view>
  
  <view class="safe-area-inset-bottom"></view>
</template>

<script setup>

import {onLoad, onReachBottom, onShareAppMessage, onShareTimeline} from "@dcloudio/uni-app";
import {ref} from "vue";
import {apiGetClassList} from "@/api/apis";

const noData = ref(false)
const classifyList = ref([])
const params = {
  pageNum: 1,
  pageSize: 12
}

let pageName;

onLoad((e) => {
  let {id = null, name = null, type = null} = e
  if(type) params.type = type;
  if(id) params.classid = id;
  pageName = name
  //修改导航标题
  uni.setNavigationBarTitle({
    title: name
  })
  
  getClassList()
})

const getClassList = async () => {
  let res;
  if (params.classid) {
    res = await apiGetClassList(params)
  }
  classifyList.value = [...classifyList.value, ...res.data]
  if (params.pageSize > res.data.length) noData.value = true;
  uni.setStorageSync("storgClassList", classifyList.value);
}


onReachBottom(() => {
  if (noData.value) return;
  params.pageNum++;
  getClassList();
})

//分享给好友
onShareAppMessage((e) => {
  return {
    title: "咸虾米壁纸-" + pageName,
    path: "/pages/classlist/classlist?id=" + params.classid + "&name=" + pageName
  }
})


//分享朋友圈
onShareTimeline(() => {
  return {
    title: "咸虾米壁纸-" + pageName,
    query: "id=" + params.classid + "&name=" + pageName
  }
})

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