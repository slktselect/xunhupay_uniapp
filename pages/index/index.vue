<template>
	<view class="uni-flex uni-column">
		<view class="flex-item uni-padding-wrap uni-common-mt">
			<view class="text-box" scroll-y="true">
				<text>{{text}}</text>
			</view>
		</view>
		<view class="flex-item uni-flex uni-row">
			<view class="currency-unit" scroll-y="true">
				<text>￥</text>
			</view>
			<view class="flex-item uni-form-item uni-column">
				<view class="uni-input-wrapper">
					<input v-model="money" :maxlength="maxlength" class="uni-input" @input="checkNum" type="digit" focus />
				</view>
			</view>
		</view>
		<button @tap="goDetail" :disabled="buttondisble" plain="true" type="primary">付款</button>
	</view>
</template>

<script>
	import {
		nextTick
	} from "vue"
	import { mapState } from 'vuex'
	export default {
		data() {
			return {
				text: "付款金额（CNY）",
				money: '',
				maxlength: 10,
				focus: false,
				buttondisble: true,
			}
		},

		methods: {
			checkNum(e) {
				let value = e.detail.value;
				let dot = value.indexOf('.'); //包含小数点
				let reg = /^[0-9]+$/; //正整数
				if (dot > -1) {
					this.maxlength = dot + 3; //长度是小数点后两位
					if (value.length > dot + 3) {}
				}
				if (reg.test(value)) { //如果是正整数不包含小数点
					this.maxlength = 10;
				}
				if (e.detail.value > 0) {
					this.buttondisble = false
				}
			},
			goDetail(){
				if (this.money <= 0) {
					this.buttondisble = true
				} else {
					uni.navigateTo({
						url: 'pay/pay?total_fee='+this.money
					})
				}
			}
			
		},
		computed: {
			...mapState({
				hasLeftWin: state => !state.noMatchLeftWindow
			})
		},
	}
</script>

<style>
	.text-box {
		margin-bottom: 40upx;
		padding: 40upx;
		display: flex;
		min-height: 30upx;
		background-color: #FFFFFF;
		align-items: center;
		text-align: center;
		font-size: 50upx;
		color: #353535;
		line-height: 1.8;
	}

	.uni-input-wrapper {
		/* #ifndef APP-NVUE
		display: flex;
		/* #endif */
		padding: 0upx 20upx;
		flex-direction: row;
		flex-wrap: nowrap;
		width: 100%;
		background-color: #FFFFFF;

	}
	.uni-flex {
		padding: 10upx 10upx;
	}
	.currency-unit {
		line-height: 160upx;
		font-size: 90upx;
		background-color: #FFFFFF;
	}
	.uni-input {
		flex: 1;
		text-align: left;
		font-size: 90upx;
		min-width: 100upx;
		background-color: #F1F3F4;
	}
	
	button {
		margin-top: 300upx;
		margin-bottom: 30upx;
		width: 60%;
	}
</style>
