<template>
	<view  :class="themeName">
		<view class="pagefankui">
			<view class="topText">扫码联系人工客服</view>
			<view class="midImg">
				<image :src="imgUrl"></image>
			</view>
			<view class="themeColor" @tap='showdianhua'>着急?直接咨询客服</view>
			<uni-popup ref="popup2" type="dialog">
				<uni-popup-dialog title="即将拨打" @confirm="confirm" :content="StoresPhone"></uni-popup-dialog>
			</uni-popup>
			<flow-button></flow-button>
		</view>
	</view>
</template>

<script>
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			uniPopup,
			flowButton
		},
		data() {
			return {
				imgUrl: uni.getStorageSync('KFQrCode'),
				StoresPhone: uni.getStorageSync('Phone'),
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		methods: {
			showdianhua() {
				this.$refs.popup2.open();
			},
			confirm() {
				uni.makePhoneCall({
					phoneNumber: this.StoresPhone,
					success: (res) => {
						console.log('调用成功!')
					},
					fail: (res) => {
						console.log('调用失败!')
					},
				});
			},
		}
	}
</script>

<style lang="less">
	.pagefankui {
		text-align: center;
		image{
			width: 200px;
			height: 200px;
		}
		.topText {
			font-size: 16px;
			margin-top: 20px;
			margin-bottom: 20px;
		}

		.midImg {
			width: 200px;
			height: 200px;
			margin: 0 auto 20px
		}
	}
</style>
