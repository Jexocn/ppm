<template>
	<view>
		<view class="bg"></view>
		<view class="content">
			<view class="entries-bg" :style="bgStyle">
				<view class="entries-box">
					<view v-for="(item, index) in entries" class="entry" :style="entryStyle(index)" @tap="entryClick(item)">
						{{item.title}}
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				entryHeight:88,
				entrySpace:28,				
				entries: [
					{
						title:'注册'
					},
					{
						title:'登录'
					},
					{
						title:'关于'
					},
				]
			}
		},
		onLoad() {
			
		},
		methods: {
			entryStyle(index) {
				if(index == 0) {
					return {'margin-top': '0rpx'};
				}
				return {'margin-top': this.entrySpace+'rpx'};
			},
			entryClick(item) {
				console.log("index entryClick", item);
				uni.navigateTo({
					url:'/pages/home/home'
				})
			}
		},
		computed: {
			bgStyle() {
				let height = this.entries.length*(this.entryHeight+this.entrySpace)*2;
				return {height: height+'rpx'}
			}
		}
	}
</script>

<style>
	.bg {
		width: 100vw;
		height: 100vh;
		background: #F5F5F5;
		position: fixed;
		z-index: -1;
	}
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100vw;
	}
	.entries-bg {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		background: #FFFFFF;
		box-shadow: 0px 1rpx 12rpx 0rpx rgba(0,0,0,0.08);
		border-radius: 20rpx;
		width: 700rpx;
		margin-top: 100rpx;
	}
	.entries-box {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
	}
	.entry {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 60%;
		height: 88rpx;
		background: #1761E9;
		border-radius: 20rpx;
		font-size: 36rpx;
		font-family: 'Source Han Sans CN';
		font-weight: bold;
		color: #FFFFFF;
		line-height: 55rpx;
	}
</style>
