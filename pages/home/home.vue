<template>
	<view>
		<view class="bg"></view>
		<view class="content">
			<view class="passwd-label">
				{{passwd}}
			</view>
			<view class="charset-list">
				<view> 字符：</view>
				<checkbox-group @change="charsetChange">
					<label v-for="(item, index) in passwdCharsets">
						<checkbox :disabled="item.disabled" :checked="item.selected" :value="index"/>{{item.title + '&nbsp&nbsp'}}
					</label>
				</checkbox-group>
			</view>
			<view class="length-box">
				<view class="length-title"> {{'长度：'+passwdLen}}</view>
				<view class="length-button" @tap="lengthAdd(-1)">-</view>
<!-- 				<view class="length-slide-bar">
					<movable-area class="length-slider-ma">
						<movable-view class="length-slider" direction="horizontal" :x="lengthX" @change="lengthSlide" :animation="false">
							{{'<>'}}
						</movable-view>
					</movable-area>
				</view> -->
				<slider :value="passwdLen" min="1" :max="passwdMaxlen" step="1" @change="sliderChange" @changing="sliderChange" style="width: 360rpx;"/>
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
					{id:0, title:'ABC', selected:false, disabled:false},
					{id:1, title:'abc', selected:false, disabled:false},
					{id:2, title:'123', selected:true, disabled:true},
					{id:3, title:'#$&', selected:false, disabled:false}
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
			charsetChange(e) {
				let checked = e.detail.value;
				let disabled = checked.length == 1;
				checked.forEach((id)=>{
					let item = this.passwdCharsets[id];
					item.selected = true;
					item.disabled = disabled;
				})
			},
			async genPasswd() {
				let randomArray = null;
				// #ifndef MP-WEIXIN
				randomArray = new Uint8Array(32);
				crypto.getRandomValues(randomArray);
				// #endif
				// #ifdef MP-WEIXIN
				wx.getRandomValues({
				  length: 32, // 生成 6 个字节长度的随机数
				  success: res => {
					randomArray = new Uint8Array(res.randomValues);
					console.log("++++ 1", randomArray);
				  }
				})
				// #endif
				console.log("++++ 2", randomArray);
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
			// lengthSlide(e) {
			// 	this.lengthOx = e.detail.x;
			// 	this.passwdLen = 1+Math.round((this.passwdMaxlen-1)*this.lengthOx/this.sliderLx);
			// },
			sliderChange(e) {
				this.passwdLen = e.detail.value;
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
		width: 160rpx;
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
