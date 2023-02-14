<template>
	<view class="uni-flex uni-column pay_way">
		<view class="flex-item _row title">选择支付方式</view>
		<radio-group @change="radioChange">
			<view flex-item class="_row way" v-for="(item, index) in paywayData" :key="item.value">
				<view class="left">
					<image class="image" :src="item.url" mode="widthFix"></image>
					<text>{{item.name}}</text>
				</view>
				<radio :value="item.value" :localdata="paywayData" :checked="index === current" />
			</view>
		</radio-group>
		<button flex-item @tap="goDetail" :disabled="buttondisble" plain="true" type="primary">付款</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				paywayData: [{
						value: 'wx',
						name: '微信支付',
						checked: 'true',
						url: '../../../static/image/wxzf.png'
					},
					{
						value: 'paypal',
						name: 'paypal支付',
						url: '../../../static/image/paypal.png'
					}
				],
				current: 0,
				buttondisble: false,
				total_fee: 0
			}
		},
		onLoad(options) {
			this.total_fee = options.total_fee
		},
		methods: {
			radioChange(e) {
				for (let i = 0; i < this.paywayData.length; i++) {
					if (this.paywayData[i].value === e.detail.value) {
						this.current = i;
						break;
					}
				}
			},
			goDetail() {

				if (this.total_fee <= 0 || this.total_fee == null) {
					this.buttondisble = true
				} else {
					if (this.current == 0) {
						window.location.href =
							'http://182.46.158.94:1234/api/create_order/?mode_of_payment=wechat&money=' + this.total_fee;
					} else {
						if (this.current == 1) {
							window.location.href =
								'http://182.46.158.94:1234/api/create_order/?mode_of_payment=paypal&money=' + this
								.total_fee;
						}
					}
				}
			}
		}
	}
</script>

<style>
	.title {
		height: 90rpx;
		line-height: 90rpx;
		font-size: 32rpx;
		border-top: 1px solid #ddd;
	}

	.left {
		display: inline-flex;
		align-items: center;
	}

	._row {
		padding: 0 100rpx;
		height: 200rpx;
		border-bottom: 1px solid #ddd;
	}

	.image {
		max-width: 320rpx;
	}

	button {
		margin-top: 300upx;
		margin-bottom: 30upx;
		width: 60%;
	}
</style>
