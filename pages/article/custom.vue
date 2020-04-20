<template>
	<view>
		<jyf-parser :html="html"></jyf-parser>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				html:""
			};
		},
		onLoad() {
			let dat = uni.getStorageSync("customdata")
			this.html = dat.data
			uni.setNavigationBarTitle({
			    title: dat.title
			});
		}
	}
</script>

<style lang="scss">

</style>
