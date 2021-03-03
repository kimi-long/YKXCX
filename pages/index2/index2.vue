<template>
	<view id="root" :class="themeName">
		<view>
			<view class="ThemedWrapper-jd76ve-0 hnMLRX slide pop-enter-done">
				<view class="SignIn__SignInWrapper-u1qscc-0 jclfjH">
					<view class="CustomSignIn__SignInWrapper-pwygxr-1 dyqwgx orange index">
						<view class="logoWrapper themeColor">绑定手机</view>
						<view class="CustomSignIn__InputWrapper-pwygxr-2 jfCxwd phone orange index">
							<view class="am-list-item am-input-item am-list-item-middle">
								<view class="am-list-line">
									<view class="am-input-control"><input placeholder="输入手机号" maxlength="11" type="number" v-model="Phone"></view>
								</view>
							</view>
						</view>
						<view class="CustomSignIn__InputWrapper-pwygxr-2 jfCxwd smsCode orange index">
							<view class="am-list-item am-input-item am-list-item-middle am-input-focus am-input-android">
								<view class="am-list-line">
									<view class="am-input-control"><input placeholder="输入验证码" maxlength="6" type="text" v-model="code"></view>
									<view class="am-input-extra"><span class="CustomSignIn__Span-pwygxr-0 fkReTG orange index"><input type="button"
											 class="themeColor" id="btn" disabled :value="codeText" @tap="clickCode($event)" /></span>
									</view>
								</view>
							</view>
						</view>
						<button role="button" @tap="clickdenglu($event)" class="themeBGCC am-button btn1 CustomButton__InnerButton-sc-1u03hwt-1 kmYrUL orange index radius am-button-small">
							<span class="inner"><span class="myicon"></span>登录</span></button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import {
		send_verify,
		Stubinding
	} from '../../static/js/env.js'
	export default {
		components: {},
		data() {
			return {
				codeText: '发送验证码',
				Phone: '',
				code: '',
				countdown2: 2,
				countdown: 60,
				themeName:'theme'+uni.getStorageSync('theme'),
			}
		},

		created() {
			uni.setNavigationBarTitle({
				title: uni.getStorageSync('storeName')
			})
			
			uni.setStorageSync('themeColor', 'rgb(72, 175, 249)');
		},

		destroyed() {

		},

		mounted() {

		},
		computed: {},
		methods: {

			Stubinding() {
				// debugger;
				//this.loading = true;
				var re = /^1\d{10}$/;
				if (re.test(this.Phone) && this.code != '') {

					uni.request({
						url: Stubinding,
						method: 'POST',
						data: {
							openid: uni.getStorageSync('openid'),
							Phone: this.Phone,
							Code: this.code,
							StoresID: uni.getStorageSync('StoresID'),
						},
						success: res => {
							let resData = res.data;
							if (resData.orsuccess == "1") {
								uni.setStorageSync('StudentID',resData.StudentID)
								uni.navigateTo({
									url: '../index/index'
								});
							} else {
								uni.showToast({
									title: resData.Msg,
									icon: 'none'
								})
							}
							if (resData.IsxcxPublic == 1 && resData.tourl != '') {
								uni.request({
									url: getXCXOpenid,
									method: 'get',
									data: {
										code: code,
										StoresID: uni.getStorageSync('StoresID')
									},
									success: res => {
										let resData = res.data;
										if (resData.orsuccess == "1") {
							
										} else {
							
										}
							
									},
									fail: () => {},
									complete: () => {}
								});
							}

						},
						fail: () => {},
						complete: () => {}
					});
				} else if (!re.test(this.Phone)) {
					uni.showToast({
						title: '请输入正确手机号码',
						icon: 'none',
						duration: 2500
					})
				} else if (this.code == '') {
					uni.showToast({
						title: '请输入正确验证码',
						icon: 'none',
						duration: 2500
					})
				}
			},
			send_verifyF() {
				// debugger;
				//this.loading = true;
				var re = /^1\d{10}$/;
				if (re.test(this.Phone)) {

					uni.request({
						url: send_verify,
						method: 'POST',
						data: {
							code: 2,
							Phone: this.Phone,
							StoresID: uni.getStorageSync('StoresID'),
						},
						success: res => {
							let resData = res.data;
							if (resData.orsuccess == "1") {
								uni.showToast({
									title: "发送成功",
									icon: 'success'
								})
							} else {
								uni.showToast({
									title: resData.Msg,
									icon: 'none'
								})
							}

						},
						fail: () => {},
						complete: () => {}
					});
				} else {
					uni.showToast({
						title: '请输入正确手机号码',
						icon: 'none',
						duration: 2500
					})
				}



			},


			clickCode(obj) {
				this.send_verifyF();
				this.settime(obj);
			},
			clickdenglu(obj) {
				this.Stubinding();
				// uni.navigateTo({
				// 	url: '../index/index'
				// });
			},
			settime2(obj) {
				if (this.countdown2 == 0) {
					obj.target.removeAttribute("disabled");
					obj.target.setAttribute("class",
						'am-button  btn1 CustomButton__InnerButton-sc-1u03hwt-1 kmYrUL orange index radius am-button-small');
					this.countdown2 = 5;
					return;
				} else {
					obj.target.setAttribute("disabled", true);
					obj.target.setAttribute("class",
						'am-button am-button-disabled btn1 CustomButton__InnerButton-sc-1u03hwt-1 kmYrUL orange index radius am-button-small'
					);
					this.codeText = "登录中";
					this.countdown2--;
				}
				this.trunsettime2(obj);

			},
			trunsettime2(obj) {
				let _this = this;
				setTimeout(function() {
					_this.settime2(obj)
				}, 1000)
			},
			settime(obj) {
				if (this.countdown == 0) {
					// obj.target.removeAttribute("disabled");
					this.codeText = "发送验证码";
					this.countdown = 60;
					return;
				} else {
					//obj.target.setAttribute("disabled", true);
					this.codeText = "重新发送(" + this.countdown + ")";
					this.countdown--;
				}
				this.trunsettime(obj);
			},
			trunsettime(obj) {
				let _this = this;
				setTimeout(function() {
					_this.settime(obj)
				}, 1000)
			},
		}
	}
</script>

<style lang="less">
	#btn {
		text-align: right;
	}
</style>
