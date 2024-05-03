<template>
	<view class="themeItem">
		<!-- //普通得专题栏 -->
		<navigator url="/pages/class-list/class-list" v-if="!isMore" class="box">
			<image class="img" src="../../common/images/classify1.jpg" mode="aspectFill"></image>
			<view class="mask">{{item.name}}</view>
			<view class="tab" v-if="compareTimestamp(item.updateTime)">{{ compareTimestamp(item.updateTime) }}前更新</view>
		</navigator>
		<!-- //更多专题栏 -->
		<navigator url="/pages/classify/classify" open-type="reLaunch" v-else class="box more">
			<image class="img" src="../../common/images/more.jpg" mode="aspectFill"></image>
			<view class="mask">
				<uni-icons type="more" size="20"></uni-icons>
				<text>更多</text>
			</view>
		</navigator>
	</view>
</template>

<script setup>
	import {
		compareTimestamp
	} from "@/utils/common.js"
	defineProps({
		isMore: {
			type: Boolean,
			default: false
		},
		item: {
			type: Object,
			default: () => {
				return {
					name: "默认名称",
					picurl: "../../common/images/classify1.jpg",
					updateTime: Date.now() - 1000 * 60 * 60 * 5
				}
			}
		}
	})
</script>

<style lang="scss" scoped>
	.themeItem {
		.box {
			height: 340rpx;
			border-radius: 10rpx;
			overflow: hidden;
			position: relative;


			.img {
				width: 100%;
				height: 100%;
			}

			.mask {
				width: 100%;
				height: 70rpx;
				position: absolute;
				bottom: 0;
				left: 0;
				background: rgba(0, 0, 0, 0.2);
				color: #fff;
				display: flex;
				align-items: center;
				justify-content: center;
				backdrop-filter: blur(20rpx);
				font-weight: 600;
				font-size: 30rpx;
			}

			.tab {
				position: absolute;
				left: 0;
				top: 0;
				background: rgba(250, 129, 90, 0.7);
				backdrop-filter: blur(20rpx);
				color: #fff;
				font-size: 22rpx;
				padding: 6rpx 14rpx;
				border-radius: 0 0 20rpx 0;
				transform: scale(0.8);
				transform-origin: left top;
			}
		}

		.box.more {
			.mask {
				width: 100%;
				height: 100%;
				flex-direction: column;	
			}

			.text {
				font-size: 28rpx;
			}
		}
	}
</style>