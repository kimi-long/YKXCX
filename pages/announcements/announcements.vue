<template>
	<view :class="themeName">
		<view class="pageAN">
			<list loadmoreoffset="10" @loadmore="loadMore">
				<view class="anListItem" v-for="(item,index) in AnnouncementList" :key='index'>
					<text>{{item.addTime}}</text>
					<view class="anListItemBox">
						<view class="title">{{item.aTitle}}</view>
						<view class="text">{{item.aInfo}}</view>
					</view>
				</view>
			</list>
			<view class="mybnWMth">已到底部</view>
			<flow-button></flow-button>
		</view>
	</view>

</template>

<script>
	import {
		getListAnnouncement
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton
		},
		data() {
			return {
				AnnouncementList: [],
				pageIndex: 0,
				finish: false,
				themeName:'theme'+uni.getStorageSync('theme'),
			};

		},
		created() {
			this.getListAnnouncementF();
		},
		methods: {
			loadMore() {
				if(!this.finish){
					this.getListAnnouncementF();
				}
			},
			getListAnnouncementF() {
				this.pageIndex = this.pageIndex + 1;
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListAnnouncement,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						pages: this.pageIndex,
						psize: "10"
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							resData.data.forEach((item, index) => {
								this.AnnouncementList.push(item);
							})
							if(resData.data.length < 10){
								this.finish = true;
							}
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
	.pageAN {
		padding: 20px;
		min-height: calc(100Vh - 40px);
	}
</style>
