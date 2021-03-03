<template>
	<view :class="themeName">
		<view class="pageNotice">
			<list loadmoreoffset="10" @loadmore="loadMore">
				<view class="anListItem" v-for="(item,index) in AnnouncementList" :key='index'>
					<text>{{item.addTime}}</text>
					<view class="anListItemBox">
						<view class="title">{{item.aTitle}}</view>
						<view class="text">{{item.SContent}}</view>
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
		StuGetListNotice
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				AnnouncementList: [],
				pageIndex: 0,
				finish: false
			};

		},
		created() {
			this.StuGetListNoticeF();
		},
		methods: {
			loadMore() {
				if(!this.finish){
					this.StuGetListNoticeF();
				}

			},
			StuGetListNoticeF() {
				this.pageIndex = this.pageIndex + 1;
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: StuGetListNotice,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
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
	.pageNotice {
		padding: 20px;
	}
</style>
