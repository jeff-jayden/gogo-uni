<template>
	<view class="classlist">
		<view class="loadingLayout" v-if="!classList.length && !noData">
			<uni-load-more status="loading"></uni-load-more>
		</view>
	</view>
</template>

<script setup>

</script>

<style lang="scss" scoped>

</style>