<template>
	<view :class="themeName">
		<view class="page">
				<view class="teacherListItem" v-for="(item,index) in teacherList" :key='index'  @tap='toTeacherDetail' :data-teacherid="item.ID">
					<view class="teacherListItemTop">
						<view class="teacherListItemImg" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}" v-if='item.HeadUrl'></view>
						<view class="teacherListItemImg" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view>
						<view class="teacherListItemInfo">
							<view class="teacherListItemInfoName">{{item.Name}}</view>
							<view class="teacherListIteminfoTitle">{{item.Title}}</view>
						</view>
					</view>
					<view class="teacherListItemInfointroduction">{{item.Introduce}}</view>
				</view>
			<flow-button></flow-button>
		</view>
	</view>

</template>

<script>
	import {
		getListTeacher
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton
		},
		data() {
			return {
				teacherList: [],
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		created() {
			this.getListTeacherF();
		},
		methods: {
			toTeacherDetail(e) {
				var teacherid = e.currentTarget.dataset.teacherid;
				uni.navigateTo({
					url: '../teacherDetail/teacherDetail?teacherid=' + teacherid
				});
			},
			getListTeacherF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListTeacher,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.teacherList = resData.data;
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
	.page {
		padding: 20px ;


	}
</style>
