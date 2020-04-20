<template>
	<view>
		<web-view :src="url"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url:""
			};
		},
		onLoad() {
			let dat = uni.getStorageSync("webviewdata")
			this.url = dat.url
			uni.setNavigationBarTitle({
			    title: dat.title
			});
		}
	}
</script>

<style lang="scss">

</style>
