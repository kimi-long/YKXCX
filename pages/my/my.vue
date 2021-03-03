<template>
	<view :class="themeName">
		<view class="page my">
			<view class="topInfo">
				<view class="headImg" :style="{backgroundImage: 'url('+numberInfo.HeadUrl+')'}"></view>
				<view class="headName">
					<text v-if="numberInfo.Name !=''">{{numberInfo.Name}}</text>
					<text v-else>{{numberInfo.wxName}}</text>
					<text class="jifen" v-if="numberInfo.isjifen" @tap='tojifen'>我的积分:{{numberInfo.integral}}<text class="iconfont iconzuo" style="font-size: 24px;"></text></text>
				</view>
				
			</view>
			<view class="fourTab">
				<view class="fourTabItem" @tap='toorder'>
					<view class="fourTabItemImg">
						<text class="iconfont iconkecheng"></text>
					</view>
					<view class="fourTabItemText">订单</view>
				</view>
				<view class="fourTabItem" @tap='tomycard'>
					<view class="fourTabItemImg">
						<text class="iconfont iconka"></text>
					</view>
					<view class="fourTabItemText">会员卡</view>
				</view>
				<view class="fourTabItem" @tap='toyuyue'>
					<view class="fourTabItemImg">
						<text class="iconfont icondingdan"></text>
					</view>
					<view class="fourTabItemText">已预约</view>
				</view>
				<view class="fourTabItem" @tap='tocomment'>
					<view class="fourTabItemImg">
						<text class="iconfont iconstar"></text>
					</view>
					<view class="fourTabItemText">评价</view>
				</view>
			</view>
			<view class="midCell">
				<view class="cellItem" @tap='toNotice'>
					<text class="iconfont icon-message"></text>
					<text class="cellItemText">消息通知</text>
					<text class="iconfont iconzuo"></text>
				</view>
				<view class="cellItem" @tap='tofankui'>
					<text class="iconfont iconfankui"></text>
					<text class="cellItemText">反馈问题</text>
					<text class="iconfont iconzuo"></text>
				</view>
				<view class="cellItem" @tap='toQA'>
					<text class="iconfont icon-help"></text>
					<text class="cellItemText">常见问题</text>
					<text class="iconfont iconzuo"></text>
				</view>
			</view>
			<view class="bottomCell">
				<view class="cellItem" @tap='toSetting'>
					<text class="iconfont icon-setting"></text>
					<text class="cellItemText">设置</text>
					<text class="iconfont iconzuo"></text>
				</view>
			</view>
			<view class="xingxing">
				<text class="themeColor">智火约课</text>
				<text>提供技术支持</text>
			</view>
					<bottom-Bar :index="indexNum"></bottom-Bar>
		</view>
	</view>

</template>

<script>
	import {
		getStudentInfo
	} from '../../static/js/env.js'
		import bottomBar from "@/components/bottomBar/bottomBar.vue";
	export default {
		components: {
			bottomBar
		},
		data() {
			return {
				indexNum:4,
				numberInfo: {
					HeadUrl: "",
					Name: "",
					wxName: "",

				},
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		created() {
			if(uni.getStorageSync('StudentID') == '0'){
				uni.reLaunch({
					url: '../login/login'
				});
			}else{
				this.getStudentInfoF();
			}

		},
		methods: {
			tocomment(){
				uni.navigateTo({
					url: '../comment/comment'
				});
			},
			tojifen(){
				uni.navigateTo({
					url: '../jifen/jifen'
				});
			},
			tomycard(){
				uni.navigateTo({
					url: '../mycard/mycard'
				});
			},
			toyuyue(){
				uni.navigateTo({
					url: '../yuyue/yuyue'
				});
			},
			toorder(){
				uni.navigateTo({
					url: '../order/order'
				});
			},
			toSetting(){
				uni.navigateTo({
					url: '../setting/setting'
				});
			},
			toQA(){
				uni.navigateTo({
					url: '../QA/QA'
				});
			},
			toNotice(){
				uni.navigateTo({
					url: '../notice/notice'
				});
			},
			tofankui(){
				uni.navigateTo({
					url: '../fankui/fankui'
				});
			},
			getStudentInfoF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getStudentInfo,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.numberInfo = resData.data;
							uni.hideLoading();
						} else {
							uni.showToast({
								title: resData.Msg,
								icon: 'none',
								duration: 2000
							})
						}

					},
					fail: () => {},
					complete: () => {}
				});
			},
		}
	}
</script>

<style lang="less">

</style>
