<template>
	<view class="homeLayout pageBG">
		<view class="banner">
			<swiper indicator-dots indicator-color="rgba(255,255,255,0.5)" indicator-active-color="rgba(255,255,255,1)"
				autoplay circular>
				<swiper-item v-for="item in bannerList" :key="item._id">
					<navigator v-if="item.target == 'miniProgram'" :url="item.url" class="like" target="miniProgram"
						:app-id="item.appid">
						<image :src="item.picurl" mode="aspectFill"></image>
					</navigator>
					<navigator v-else :url="`/pages/classlist/classlist?${item.url}`" class="like">
						<image :src="item.picurl" mode="aspectFill"></image>
					</navigator>
				</swiper-item>
			</swiper>
		</view>
		<view class="notice">
			<view class="left">
				<uni-icons type="sound-filled" size="20"></uni-icons>
				<text class="text">公告</text>
			</view>
			<view class="center">
				<swiper vertical circular :autoplay="true" :interval="1500" :duration="300">
					<swiper-item>
						<view class="swiper-item">轮播1sdasdaaaaaaaaaaaaa斯蒂芬哈撒旦发射点金卡回复</view>
					</swiper-item>
					<swiper-item>
						<view class="swiper-item">轮播2可是对方哈师大和福克斯的就会发生会计法</view>
					</swiper-item>
				</swiper>
			</view>
			<view class="right">
				<uni-icons type="right" size="16" color="#333"></uni-icons>
			</view>
		</view>
		<view class="select">
			<common-title>
				<template #name>
					<view>每日推荐</view>
				</template>
				<template #custom>
					<view class="date">
						<uni-icons type="calendar" size="18" color="$brand-theme-color"></uni-icons>
						<view class="text">
							<uni-dateformat :date="Date.now()" format="dd日"></uni-dateformat>
						</view>
					</view>
				</template>
			</common-title>
			<view class="content">
				<scroll-view scroll-x>
					<view class="box" v-for="item in 6">
						<image src="../../common/images/preview_small.webp" mode="aspectFill"></image>
					</view>
				</scroll-view>
			</view>
		</view>
		<view class="theme">
			<common-title>
				<template #name>
					<view>专题精选</view>
				</template>
				<template #custom>
					<navigator url="#" class="more">More+</navigator>
				</template>
			</common-title>
			<view class="content">
				<theme-item v-for="item in classifyList" :key="item._id" :item=item></theme-item>
				<theme-item :is-more="true"></theme-item>
			</view>
		</view>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue';
	import {
		onShareAppMessage,
		onShareTimeline
	} from "@dcloudio/uni-app"
	import {
		apiGetBanner,
		apiGetDayRandom,
		apiGetNotice,
		apiGetClassify
	} from "@/api/apis.js"

	const bannerList = ref([]);
	const randomList = ref([]);
	const noticeList = ref([]);
	const classifyList = ref([]);

	const getBanner = async () => {
		let res = await apiGetBanner();
		bannerList.value = res.data;
		console.log(bannerList.value);
	}

	const getDayRandom = async () => {
		let res = await apiGetDayRandom();
		randomList.value = res.data
	}

	const getNotice = async () => {
		let res = await apiGetNotice({
			select: true
		});
		noticeList.value = res.data
	}

	const getClassify = async () => {
		let res = await apiGetClassify({
			select: true
		});
		classifyList.value = res.data
		console.log('classifyList' + classifyList.value);
	}

	getBanner();
	getDayRandom();
	getNotice();
	getClassify();
</script>

<style lang="scss" scoped>
	.homeLayout {

		.banner {
			width: 750rpx;
			padding: 30rpx 0;

			swiper {
				width: 750rpx;
				height: 340rpx;

				&-item {
					padding: 0 30rpx;
					width: 100%;
					height: 100%;

					.like {
						width: 100%;
						height: 100%;

						image {
							width: 100%;
							height: 100%;
							border-radius: 10rpx;
						}
					}
				}
			}
		}

		.notice {
			margin: 0 auto;
			width: 690rpx;
			height: 80rpx;
			line-height: 80rpx;
			background-color: #f9f9f9;
			display: flex;
			border-radius: 80rpx;

			.left {
				width: 140rpx;
				display: flex;
				align-items: center;
				justify-content: center;

				:deep() {
					.uni-icons {
						color: $brand-theme-color !important;
					}
				}

				.text {
					color: $brand-theme-color;
					font-weight: 600;
					font-size: 28rpx;
				}
			}

			.center {
				flex: 1;

				swiper {
					height: 100%;

					&-item {
						height: 100%;
						font-size: 30rpx;
						color: #666;
						overflow: hidden;
						white-space: nowrap;
						text-overflow: ellipsis;
					}
				}
			}

			.right {
				width: 70rpx;
				display: flex;
				align-items: center;
				justify-content: center;
			}
		}

		.select {
			padding-top: 50rpx;

			.content {
				width: 720rpx;
				margin-top: 30rpx;
				margin-left: 30rpx;

				scroll-view {
					white-space: nowrap;

					.box {
						display: inline-block;
						width: 200px;
						height: 430px;
						margin-right: 15rpx;

						image {
							width: 100%;
							height: 100%;
							border-radius: 10rpx;
						}

						&:last-child {
							margin-right: 30rpx;
						}
					}
				}
			}

			.date {
				display: flex;
				align-items: center;
				justify-content: center;

				.text {
					color: $brand-theme-color;
					margin-left: 10rpx;
				}
			}
		}

		.theme {


			padding: 50rpx 0;

			.more {
				font-size: 32rpx;
				color: #788aa2;
			}

			.content {
				display: grid;
				margin-top: 30rpx;
				padding: 0 30rpx;
				gap: 15rpx;
				grid-template-columns: repeat(3, 1fr);
			}
		}

	}
</style>