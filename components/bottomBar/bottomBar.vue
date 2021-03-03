<template>
	<view class="am-tabs-tab-bar-wrap" style="position:fixed;left:0;bottom:0;width:100%;z-index:99;background-color: #fff;overflow: hidden;" :class="themeName">
		<view class="am-tab-bar-bar bottom" style="width: 100%;overflow: hidden;">
			<view class="am-tab-bar-tab" @tap="toIndex" :class="index == 1?'current':''">
				<view class="am-tab-bar-tab-icon">
					<view class="IndexNavBar__BadgeWrapper-zp8p8u-1 fXTpFr">
						<text class="iconfont icon-shop"></text>
					</view>
				</view>
				<p class="am-tab-bar-tab-title">首页</p>
			</view>
			<view class="am-tab-bar-tab" @tap="tocalendar" :class="index == 2?'current':''">
				<view class="am-tab-bar-tab-icon">
					<view class="IndexNavBar__BadgeWrapper-zp8p8u-1 fXTpFr"><text class="iconfont iconkebiao1"></text></view>
				</view>
				<p class="am-tab-bar-tab-title">课表</p>
			</view>
			<view class="am-tab-bar-tab" @tap="toyuyue" :class="index == 3?'current':''">
				<view class="am-tab-bar-tab-icon">
					<view class="IndexNavBar__BadgeWrapper-zp8p8u-1 fXTpFr"><text class="iconfont iconclock"></text></view>
				</view>
				<p class="am-tab-bar-tab-title">已约</p>
			</view>
			<view class="am-tab-bar-tab" @tap="tomy" :class="index == 4?'current':''">
				<view class="am-tab-bar-tab-icon">
					<view class="IndexNavBar__BadgeWrapper-zp8p8u-1 fXTpFr"><text class="iconfont icon-staff"></text></view>
				</view>
				<p class="am-tab-bar-tab-title">我的</p>
			</view>
		</view>
	</view>
</template>

<script>
	// import { getLocal,getStore,setStore } from '@/config/mUtils';

	export default {
		components: {},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
			}
		},
		props: ['index'],
		created() {},

		destroyed() {

		},

		mounted() {
			// jQuery('.am-tabs-default-bar-tab').click(function() {
			// 	TabSelect(".am-tabs-default-bar-tab", "view.lists", "am-tabs-default-bar-tab-active", jQuery(this))
			// });
			// jQuery('.am-tabs-default-bar-tab').eq(0).trigger("click");

			// function TabSelect(tab, con, addClass, obj) {
			// 	var jQuery_self = obj;
			// 	var jQuery_nav = jQuery(tab);

			// 	jQuery_nav.removeClass(addClass),
			// 		jQuery_self.addClass(addClass);

			// 	var jQuery_index = jQuery_nav.index(jQuery_self);
			// 	var jQuery_con = jQuery(con);
			// 	jQuery_con.hide(),
			// 		jQuery_con.eq(jQuery_index).show();
			// };
		},
		computed: {},
		methods: {
			toIndex() {
				if (this.index != 1) {
					uni.reLaunch({
						url: '../../pages/homepage/homepage'
					});
				}

				//   this.$router.push({path:'/',query:{storesid:getStore('storesid'),openid:getStore('openid')}})
			},
			tocalendar() {
				if (this.index != 2) {
					uni.reLaunch({
						url: '../../pages/rili/rili'
					});
				}
				// this.$router.push({path:'/calendar',query:{storesid:getStore('storesid'),openid:getStore('openid')}})
			},
			toyuyue() {
				if (this.index != 3) {
					uni.reLaunch({
						url: '../../pages/yuyue/yuyue'
					});
				}
				// this.$router.push({path:'/yuyue',query:{storesid:getStore('storesid'),openid:getStore('openid')}})
			},
			tomy() {
				if (this.index != 4) {
					uni.reLaunch({
						url: '../../pages/my/my'
					});
				}

				//this.$router.push({path:'/my',query:{storesid:getStore('storesid'),openid:getStore('openid')}})
			},

		}
	}
</script>

<style lang="less" scoped>
	.iconfont {
		font-size: 30px;
	}

	view {
		line-height: 1 !important;
	}
</style>
