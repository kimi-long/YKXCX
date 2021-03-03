<template>
		<view :class="themeName">
			<view class="Trili">
				<view class="riliBox">
					<zzx-calendar @selected-change="datechange"></zzx-calendar>
				</view>
				<view class="tabList">
					<view class="tabListItem" :class="activeIndex == 0?'active':''" @tap='changeTab(0)'>全部</view>
					<view class="tabListItem" :class="activeIndex == 1?'active':''" @tap='changeTab(1)'>班课</view>
					<view class="tabListItem" :class="activeIndex == 2?'active':''" @tap='changeTab(2)'>私教</view>
				</view>
				<view class="todayBox">{{dateTime}}的课程安排</view>
				<view class="keList" v-if='RowClassTwo.length > 0'>
						<view class="riliKeListItem" v-for="(item,index) in RowClassTwo" @tap='toLesson' :data-showstate="item.showstate" :data-RowClassID="item.ID" :data-PreAboutID="item.PreAboutID">
							<view class="riliKeListItemImg" :style="{backgroundImage: 'url(' + item.CourseHeadUrl + ')'}">
								<text class="riliKeListItemImgText" v-if="item.showstate == '1'">不可约</text>
								<text class="riliKeListItemImgText" v-if="item.showstate == '0'">可预约</text>
							</view>
							<view class="riliKeListItemInfo">
								<view class="firstLine">
									<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '0'">班课</text>
									<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '1'">私教</text>
									<text class="riliKeListItemInfoName">{{item.CourseName}}</text>
								</view>
								<view class="riliKeListItemInfoTime">
									{{item.startTime}}
								</view>
								<view class="riliKeListItemInfoClassTime">
									<text class="riliKeListItemInfoClassTime" v-if='item.ConsumptionOfClass'>{{item.ConsumptionOfClass}}课时</text>
									<text class="riliKeListItemInfoClassPrice"  v-if="Math.round(item.singlePrice) != 0">￥{{item.singlePrice}}</text>
								</view>
								<view class="riliKeListItemInfoClassTeacher">
								{{item.Name}}
								<text class="can" v-if="item.showstate == 0">预约</text>
								<text class="cannot" v-if="item.showstate == 1">已结束</text>
								</view>
							</view>
						</view>
						<view style="height: 50px;"></view>
				</view>
				<view class="kong" v-else>
					<view class="iconfont iconkong themeColor"></view>
					<view class="zanwu">暂无数据</view>
				</view>
			</view>
		</view>

</template>

<script>
	import {
		getListTeacherRowClass
	} from '../../static/js/env.js'
	import zzxCalendar from "@/components/zzx-calendar/zzx-calendar.vue"
	import bottomBar from "@/components/bottomBar/bottomBar.vue";
	export default {
		components: {
			zzxCalendar,
			bottomBar
		},
		data() {
			return {
				dateTime: '',
				teacherid:'',
				courseid:'',
				indexNum: 2,
				activeIndex: 0,
				RowClassTwo: [],
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		onLoad: function(e) {
			console.log(e);
			this.teacherid = e.teacherid;
			this.courseid = e.courseid
			let today = new Date();
			let year = today.getFullYear();
			let month = today.getMonth() + 1;
			let day = today.getDate();
			this.dateTime = year + '-' + month + '-' + day;
			this.getListTeacherRowClassF();
		},
		created() {

		},
		methods: {
			toLesson(e){
				if(e.currentTarget.dataset.showstate == '0'){
					console.log(e)
					var RowClassID = e.currentTarget.dataset.rowclassid;
					var PreAboutID = e.currentTarget.dataset.PreAboutID;
					uni.navigateTo({
						url: '../kechengDetail/kechengDetail?RowClassID='+RowClassID+'&type=5'
					});
				}

			},
			changeTab(index) {
				this.activeIndex = index;
				this.getListTeacherRowClassF();
			},
			datechange(e) {
				console.log(e);
				this.dateTime = e.fullDate;
				this.getListTeacherRowClassF();
			},
			getListTeacherRowClassF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListTeacherRowClass,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						RowClassType: this.activeIndex,
						dateTime: this.dateTime,
						TeacherID: this.teacherid,
						CourseID: this.courseid,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.RowClassTwo = resData.data;
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
