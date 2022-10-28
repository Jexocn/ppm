<template>
	<view>
		<view class="bg"></view>
		<view class="content">
			<view class="passwd-label">
				{{passwd}}
			</view>
			<view class="charset-list">
				<view> 字符：</view>
				<view v-for="(item, index) in passwdCharsets" class="charset-item">
					<view class="charset-check" @tap="charsetCheck(item)">{{item.selected ? selectedMark : ''}}</view>
					<view>{{item.title}}</view>
				</view>
			</view>
			<view class="length-box">
				<view class="length-title"> {{'长度：'+passwdLen}}</view>
				<view class="length-button" @tap="lengthAdd(-1)">-</view>
				<view class="length-slide-bar">
					<movable-area class="length-slider-ma">
						<movable-view class="length-slider" direction="horizontal" :x="lengthX" @change="lengthSlide" :animation="false">
							{{'<>'}}
						</movable-view>
					</movable-area>
				</view>
				<view class="length-button" @tap="lengthAdd(1)">+</view>
			</view>
			<view class="gen-button" @tap="genPasswd">生成密码</view>
		</view>
	</view>
</template>

<script>
	import cryptoJS from 'crypto-js';
	
	export default {
		data() {
			return {
				passwd:'abc',
				passwdCharsets: [
					{id:0, title:'ABC', selected:false},
					{id:1, title:'abc', selected:false},
					{id:2, title:'123', selected:true},
					{id:3, title:'#$&', selected:false}
				],
				selectedMark: '√',
				passwdLen:1,
				passwdMaxlen: 50,
				lengthOx: 0,
				lengthX: 0,
				sliderLx: 1
			}
		},
		methods: {
			charsetCheck(item) {
				if(item.selected) {
					let n = 0;
					this.passwdCharsets.forEach((v)=>n+=v.selected);
					if(n == 1) {
						return
					}
				}
				item.selected = !item.selected;
			},
			genPasswd() {
				// #ifndef MP-WEIXIN
				let a = new Uint32Array(4);
				console.log("++++", crypto.getRandomValues(a));
				// #endif
				// #ifdef MP-WEIXIN
				wx.getRandomValues({
				  length: 16, // 生成 6 个字节长度的随机数
				  success: res => {
				    console.log(wx.arrayBufferToBase64(res.randomValues)) // 转换为 base64 字符串后打印
				  }
				})
				// #endif
			},
			lengthAdd(add) {
				this.passwdLen += add;
				if(this.passwdLen < 1) {
					this.passwdLen = 1;
				} else if (this.passwdLen > this.passwdMaxlen){
					this.passwdLen = this.passwdMaxlen;
				}
				this.lengthX = Math.round((this.passwdLen-1)/(this.passwdMaxlen-1)*this.sliderLx);
			},
			lengthSlide(e) {
				this.lengthOx = e.detail.x;
				this.passwdLen = 1+Math.round((this.passwdMaxlen-1)*this.lengthOx/this.sliderLx);
			}
		},
		created() {
			const sysInfo = uni.getSystemInfoSync();
			this.sliderLx = Math.round(sysInfo.windowWidth*340/750)-2;
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
		width: 100vw;
	}
	.passwd-label {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 700rpx;
		height: 96rpx;
		background: #FFFFFF;
		box-shadow: 1rpx 1rpx 13rpx 0rpx rgba(0,0,0,0.08);
		border-radius: 20rpx;
		font-size: 56rpx;
		font-family: 'Source Han Sans CN';
		font-weight: 400;
		color: #333333;
		margin-top: 20rpx;
	}
	.charset-list {
		display: flex;
		align-items: center;
		justify-content: space-around;
		width: 700rpx;
		font-size: 32rpx;
		font-family: 'Source Han Sans CN';
		font-weight: 600;
		color: #333333;
		margin-top: 40rpx;
	}
	.charset-item {
		display: flex;
		align-items: center;
		justify-content: flex-end;
		width: 150rpx;
	}
	.charset-check {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 30rpx;
		height: 30rpx;
		border: 1rpx solid #333333;
		margin-right: 10rpx;
	}
	.gen-button {
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
		margin-top: 50rpx;
	}
	.length-box {
		display: flex;
		align-items: center;
		justify-content: space-around;
		width: 700rpx;
		font-size: 32rpx;
		font-family: 'Source Han Sans CN';
		font-weight: 600;
		text-align: center; 
		color: #333333;
		margin-top: 40rpx;
	}
	.length-title {
		display: flex;
		align-items: center;
		width: 140rpx;
	}
	.length-button {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 60rpx;
		height: 60rpx;
		border: 1rpx solid #333333;
		border-radius: 30rpx;
	}
	.length-slide-bar {
		display: flex;
		align-items: center;
		width: 400rpx;
		height: 6rpx;
		border: 1rpx solid #333333;
		border-radius: 6rpx;
		overflow-y: visible;
	}
	.length-slider-ma {
		width: 100%;
		height: 60rpx;
	}
	.length-slider {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 60rpx;
		height: 60rpx;
		background-color: #FFFFFF;
		border: 1rpx solid #1761E9;
		border-radius: 30rpx;
	}
</style>
