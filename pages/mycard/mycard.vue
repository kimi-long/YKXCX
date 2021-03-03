<template>
		<view :class="themeName">
			<view class="pagemycard">
				<list v-if='courseList.length > 0'>
					<view class="coureseListItem" v-for="(item,index) in courseList" :key='index'>
						<view class="coureseListItemImgCard" :style="{backgroundImage: 'url(' + item.CradImgURL + ')'}" v-if="item.CradImgURL != '' "></view>
						<view class="coureseListItemImgCard" style="background-image: url(../../static/images/bluestyle/morenka.png);" v-else></view>
						<view class="coureseListItemInfo">
							<view class="coureseListItemInfoNameAll"  style='line-height: 1.8;'>
								<text class="coureseListItemInfoName">{{item.CardName}} <text v-if="item.isStop == '1' ">(停卡)</text></text>
							</view>
							<view class="coureseListItemInfosuitPeople" style='line-height: 1.6;'>
								<view v-if="item.CardType == '1' "  style='line-height: 1.6;'>有效期：{{item.OverdueTime}}</view>
								<view v-if="item.CardType == '1' "  style='line-height: 1.6;'>可用次数：{{item.CardNumber}}</view>
								<view v-if="item.CardType == '2' "  style='line-height: 1.6;'>有效期：不限期</view>
								<view v-if="item.CardType == '2' " style='line-height: 1.6;'>可用次数：{{item.CardNumber}}</view>
								<view v-if="item.CardType == '3' " style='line-height: 1.6;'>有效期：{{item.OverdueTime}}</view>
								<view v-if="item.CardType == '3' " style='line-height: 1.6;'>可用次数：不限次数 </view>
								<view v-if="item.CardType == '4' " style='line-height: 1.6;'>有效期：{{item.OverdueTime}}</view>
								<view v-if="item.CardType == '4' " style='line-height: 1.6;'>可用金额：{{item.Amount}}</view>
							</view>
							<view class="coureseListItemInfolevel" style='line-height: 1.6;'>
								<text v-if="item.CardType == 4">￥{{item.MemberCardinfo.SellingPrice}}</text>
								<text v-else>￥{{item.PurchaseAmount}}</text>
							</view>
						</view>
					</view>
				</list>
				<view class="kong" v-else>
					<view class="iconfont iconkong themeColor"></view>
					<view class="zanwu">暂无数据</view>
				</view>
				<flow-button></flow-button>
			</view>
		</view>

</template>

<script>
	import {
		getwxListMembersvip
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton
		},
		data() {
			return {
				courseList: [],
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		created() {
			this.getwxListMembersvipF();
		},
		methods: {
			getwxListMembersvipF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getwxListMembersvip,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						StudentID: uni.getStorageSync('StudentID'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.courseList = resData.data;
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
		.kong{
			width: 100%;
			text-align: center;
			.iconfont{
				font-size: 40px;
			}
			.zanwu{
				font-size: 18px;
				color: #fff;
			}
		}
</style>
