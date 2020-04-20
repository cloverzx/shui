<script>
	export default {
		globalData:{
			url:"接口域名",//接口地址
			header:{}
		},
		onLaunch: function() {
			//#ifdef H5
			getApp().globalData.header = {
				'content-type':'application/x-www-form-urlencoded'
			}
			//#endif
		},
		onShow: function() {
		},
		onHide: function() {
		}
	}
</script>

<style>
	/*页面公共css */
</style>
