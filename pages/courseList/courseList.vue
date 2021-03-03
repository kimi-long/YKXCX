<template>
	<view :class="themeName">
		<view class="pagecourseList">
			<list>
				<view class="coureseListItem" v-for="(item,index) in courseList" :key='index' @tap='toCourseDetail' :data-courseid="item.ID">
					<view class="coureseListItemImg" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}"></view>
					<view class="coureseListItemInfo">
						<view class="coureseListItemInfoNameAll">
							<text v-if="item.isJP=='1'" class="themeTag">精品课</text>
							<text class="coureseListItemInfoName">{{item.CourseName}}</text>
						</view>
						<view class="coureseListItemInfosuitPeople">适合人群：{{item.SuitsTheCrowd}}</view>
						<view class="coureseListItemInfolevel">{{item.CourseIntroduction}}</view>
					</view>
				</view>
			</list>
			<flow-button></flow-button>
		</view>
	</view>

</template>

<script>
	import {
		getListCourse
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
			this.getListCourseF();
		},
		methods: {
			toCourseDetail(e) {
				var courseid = e.currentTarget.dataset.courseid;
				uni.navigateTo({
					url: '../courseDetail/courseDetail?courseid=' + courseid
				});
			},
			getListCourseF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListCourse,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
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

</style>
