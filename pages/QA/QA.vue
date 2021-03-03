<template>
	<view :class="themeName">
		<view class="QA">
			<uni-collapse accordion="true">
				<uni-collapse-item v-for='(item,index) in AnnouncementList' :title="item.QTitle" @change='changeIndex(index)' :open='activeIndex == index'>
					<view style="padding: 30rpx;" class="collapseitem">
						{{item.Qinfo}}
					</view>
				</uni-collapse-item>
			</uni-collapse>
			<flow-button></flow-button>
		</view>
	</view>
</template>

<script>
	import {
		getListQuestions
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	import uniCollapse from '@/components/uni-collapse/uni-collapse.vue'
	import uniCollapseItem from '@/components/uni-collapse-item/uni-collapse-item.vue'
	export default {
		components: {
			uniCollapse,
			uniCollapseItem,
			flowButton
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				AnnouncementList: [],
				activeIndex:0,
			};
		},
		created() {
			this.getListQuestionsF();
		},
		methods: {
			changeIndex(index){
				this.activeIndex = index;
			},
			getListQuestionsF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListQuestions,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.AnnouncementList = resData.data;
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
.QA{
	min-height: 100VH;
}
</style>
