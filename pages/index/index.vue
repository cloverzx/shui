<template>
	<view class="index-page">
		<uni-search-bar class="usb" @confirm="search" :radius="100" @cancel="cancel"></uni-search-bar>
		<mescroll-uni ref="mescrollRef" @init="mescrollInit" @down="downCallback" @up="upCallback" :down="downOption" :top="top" :up="upOption">
			<view v-for="(data, index) in dataList" :key="index" class="list" @tap="nav(data)">
				<view class="list-image">
					<image v-if="data.img_image != null" :src="baseurl + data.img_image" class="list-image"></image>
					<image v-else src="@/static/image/default.jpg" class="list-image"></image>
				</view>
				<view class="list-base">
					<view class="list-title">{{ data.title }}</view>
					<view class="list-other">
						<text>{{ data.keyword }}</text>
						<text>{{ data.add_time.substr(0,data.add_time.length-3) }}</text>
					</view>
				</view>
			</view>
		</mescroll-uni>
	</view>
</template>

<script>
import MescrollMixin from '@/components/mescroll-uni/mescroll-mixins.js';
export default {
	mixins: [MescrollMixin], // 使用mixin
	data() {
		return {
			// 上拉加载的配置
			upOption: {
				page: {
					num: 0, // 当前页码
					size: 10 // 每页数据的数量
				},
				noMoreSize: 5,
				textNoMore: '暂无更多数据了!',
				empty: {
					tip: '暂无相关数据'
				}
			},
			keyword: '', //搜索关键字
			dataList: [], // 列表数据
			baseurl: getApp().globalData.url,
			top:"80"
		};
	},
	onLoad() {
		let view = uni.createSelectorQuery().in(this).select(".usb");
		view.fields({
		  size: true
		}, data => {
			this.top = data.height * 2 - 5
		}).exec();
	},
	methods: {
		//跳转文章页
		nav(d) {
			if(d.type == "公众号" || d.type == "自定义链接"){
				uni.setStorageSync("webviewdata",{
					url:d.url,
					title:d.title
				})
				uni.navigateTo({
					url: '../article/webview'
				});
			}
			if(d.type == "自定义文章"){
				uni.setStorageSync("customdata",{
					data:d.content,
					title:d.title
				})
				uni.navigateTo({
					url: '../article/custom'
				});
			}
		},
		upCallback(page) {
			uni.request({
				url: getApp().globalData.url + '接口地址',
				header:getApp().globalData.header,
				data: {
					keyword: this.keyword,
					page: page.num,
					limit: page.size,
					type: this.type
				},
				success: data => {
					if (data.statusCode == 200 || data.data.code == 1) {
						let d = data.data.data.data; //获取数据
						let t = data.data.data.total; //获取数据总量
						if (page.num == 1) this.dataList = []; //第一页处理
						this.dataList = this.dataList.concat(d); //追加数据
						this.mescroll.endByPage(d.length, t); //判断是否有下一页
						this.$nextTick(() => {
							this.mescroll.endSuccess(d.length);
						});
					} else {
						uni.showToast({
							icon: 'none',
							title: '获取数据失败!',
							duration: 2500
						});
						this.mescroll.endErr();
					}
				},
				fail: () => {
					uni.showToast({
						icon: 'none',
						title: '获取数据失败!',
						duration: 2500
					});
					this.mescroll.endErr();
				}
			});
		},
		//关键字搜索
		search(e){
			this.keyword = e.value
			this.mescroll.resetUpScroll()
		},
		//取消关键字
		cancel(){
			this.keyword = ""
			this.mescroll.resetUpScroll()
		}
	},
	computed: {
		type() {
			//#ifdef H5
			return 'h5';
			//#endif
			//#ifdef MP-WEIXIN
			return 'wx';
			//#endif
			//#ifdef MP-QQ
			return 'qq';
			//#endif
		}
	}
};
</script>

<style lang="scss">
.list {
	border-bottom: 1px solid #ebebeb;
	margin: 0 36rpx;
	padding: 20rpx 0;
	display: flex;
	height: 140rpx;
	.list-image {
		width: 180rpx;
		height: 140rpx;
	}
	.list-base {
		width: 100%;
		margin-left: 22rpx;
		.list-title {
			height: 80rpx;
			line-height: 40rpx;
			overflow: hidden;
			text-overflow: ellipsis;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			line-clamp: 2;
			-webkit-box-orient: vertical;
		}
		.list-other {
			font-size: 28rpx;
			color: #a6a6a6;
			margin-top: 20rpx;
			line-height: 40rpx;
			display: flex;
			justify-content: space-between;
		}
	}
}
</style>
