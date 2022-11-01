<template>
	<view>
		<view class="bg"></view>
		<view class="content">
			<view class="passwd-label">
				<text>{{passwd}}</text>
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
				passwd:'',
				passwdCharsets: [
					{title:'ABC', selected:false, size:26, disabled:false},
					{title:'abc', selected:false, size:26, disabled:false},
					{title:'123', selected:true, size:10, disabled:true},
					{title:'#$&', selected:false, size:28, disabled:false}
				],
				passwdLen:1,
				passwdMaxlen: 50,
				specSymbols:'~!@#$%^&*()_-+={[}]|:;<,>.?/',
				name:'google.com',
				key:'123456',
			}
		},
		methods: {
			charsetChange(e) {
				let checked = e.detail.value;
				let disabled = checked.length == 1;
				this.passwdCharsets.forEach((item, idx)=>{
					item.selected = checked.includes(idx);
					item.disabled = disabled && item.selected;
				});
			},
			getChar(setId, charIdx) {
				let charset = this.passwdCharsets[setId];
				charIdx = charIdx % charset.size;
				if(setId == 3) {
					return this.specSymbols[charIdx];
				}
				if(setId == 2) {
					return String.fromCharCode('0'.charCodeAt(0)+charIdx);
				}
				return String.fromCharCode(charset.title.charCodeAt(0)+charIdx);
			},
			genPasswd() {
				let len = this.passwdLen;
				let selected = [];
				this.passwdCharsets.forEach((v, k)=>{
					if(v.selected) {
						selected.push(k);
					}
				});
				let p = new Promise((resolve, reject) => {
					let randomArray = null;
					// #ifndef MP-WEIXIN
					randomArray = new Uint8Array(len*3);
					crypto.getRandomValues(randomArray);
					resolve(randomArray);
					// #endif
					// #ifdef MP-WEIXIN
					wx.getRandomValues({
					  length: len*3,
					  success: res => {
						randomArray = new Uint8Array(res.randomValues);
						resolve(randomArray);
					  },
					  fail: () => {
						  reject("wx.getRandomValues fail");
					  }
					})
					// #endif
				})
				p.then((randomArray)=>{
					let buf = new ArrayBuffer(64);
					let i32View = new Int32Array(buf);
					cryptoJS.SHA3(this.name+this.key, {outputLength:512}).words.forEach((v, k)=>{
						i32View[k] = v
					});
					let u8View = new Uint8Array(buf);
					let pwd = [];
					for (let k=0; k < len; k++) {
						let charIdx = u8View[randomArray[k]%64] ^ randomArray[len+k];
						let setId = selected[randomArray[2*len+k]%selected.length];
						pwd.push(this.getChar(setId, charIdx));
					}
					this.passwd = pwd.join('');
				})
			},
			lengthAdd(add) {
				this.passwdLen += add;
				if(this.passwdLen < 1) {
					this.passwdLen = 1;
				} else if (this.passwdLen > this.passwdMaxlen){
					this.passwdLen = this.passwdMaxlen;
				}
			},
			sliderChange(e) {
				this.passwdLen = e.detail.value;
			}
		},
		created() {
			
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
		width: 680rpx;
		height: 288rpx;
		background: #FFFFFF;
		box-shadow: 1rpx 1rpx 13rpx 0rpx rgba(0,0,0,0.08);
		border-radius: 20rpx;
		font-size: 56rpx;
		font-family: 'Source Han Sans CN';
		font-weight: 400;
		color: #333333;
		margin-top: 20rpx;
		padding-left: 20rpx;
		padding-right: 10rpx;
		word-break: break-all;
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
</style>
