<template>
	<view>
		<!-- #ifdef MP-WEIXIN -->
		<view v-if="isCanUse">
			<view>
				<view class='header'>
					<image src='../../static/images/bluestyle/shouquan.png'></image>
				</view>
				<view class='content'>
					<view>申请获取以下权限</view>
					<text>获得你的公开信息(昵称，头像、地区等)</text>
				</view>

				<button class='bottom' type='primary' open-type="getUserInfo" withCredentials="true" lang="zh_CN" @getuserinfo="wxGetUserInfo">
					授权登录
				</button>
			</view>
		</view>
		<!-- #endif -->
	</view>
</template>

<script>
	import {
		sendXCXOpenid
	} from '../../static/js/env.js'
	export default {
		data() {
			return {
				SessionKey: '',
				OpenId: '',
				nickName: null,
				avatarUrl: null,
				isCanUse: uni.getStorageSync('isCanUse') || true //默认为true
			};
		},
		methods: {
			//第一授权获取用户信息===》按钮触发
			wxGetUserInfo() {
				let _this = this;
				uni.getUserInfo({
					provider: 'weixin',
					success: function(infoRes) {
						console.log()
						let nickName = infoRes.userInfo.nickName; //昵称
						uni.setStorageSync('wx_name', nickName);
						let avatarUrl = infoRes.userInfo.avatarUrl; //头像
						uni.setStorageSync('wx_topimg', avatarUrl);
						try {
							uni.setStorageSync('isCanUse', false); //记录是否第一次授权  false:表示不是第一次授权
							_this.updateUserInfo();
						} catch (e) {}
					},
					fail(res) {}
				});
			}, //登录

			//向后台更新信息
			updateUserInfo() {
				let _this = this;
				uni.request({
					url: sendXCXOpenid, //服务器端地址
					data: {
						openid: uni.getStorageSync('openid'),
						wx_name: uni.getStorageSync('wx_name'),
						wx_topimg: uni.getStorageSync('wx_topimg'),
						StoresID: uni.getStorageSync('StoresID'),
					},
					method: 'POST',
					header: {
						'content-type': 'application/json'
					},
					success: (res) => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: "授权成功",
								icon: 'success'
							})
							setTimeout(function() {
									uni.navigateTo({
										url: '../index2/index2'
									});
								},
								1000);

						}
					}

				});
			}
		},
		onLoad() { //默认加载
			uni.setNavigationBarTitle({
				title: uni.getStorageSync('storeName')
			})
		}
	}
</script>

<style>
	.header {
		margin: 90rpx 0 90rpx 50rpx;
		border-bottom: 1px solid #ccc;
		text-align: center;
		width: 650rpx;
		height: 300rpx;
		line-height: 450rpx;
	}

	.header image {
		width: 200rpx;
		height: 200rpx;
	}

	.content {
		margin-left: 50rpx;
		margin-bottom: 90rpx;
	}

	.content text {
		display: block;
		color: #9d9d9d;
		margin-top: 40rpx;
	}

	.bottom {
		border-radius: 80rpx;
		margin: 70rpx 50rpx;
		font-size: 35rpx;
		line-height: 80rpx;
	}
</style>
