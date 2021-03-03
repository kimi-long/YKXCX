<template>
	<view :class="themeName">
		<view class="courseDetail" >
			<view class="courseDetailTop">
				<view class="toplunbo">
					<swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration"
					 :circular='true'>
						<swiper-item v-for="(item,index) in course.listSlideshow">
							<cover-image :src="item.ImgUrl"></cover-image>
						</swiper-item>
					</swiper>
				</view>
		
			</view>
			<view class="courseDetailInfo">
				<view class="courseNameTitle">{{course.CourseName}}</view>
				<view class="courseLevel">
					<text class="title">等级</text>
					<text>{{course.LevelName}}</text>
				</view>
				<view class="coursehard">
					<text class="title">难度</text>
					<text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >0 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >1 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >2 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >3 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >4 ? 'themeColor':'starHui'"></text>
					</text>
				</view>
			</view>
			<view class="courseDetailBox">
				<view class="courseDetailTitle">任课老师</view>
				<view class="teacherItem" v-for="(item,index) in course.listTeacher">
					<view class="teacherHead" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}" v-if='item.HeadUrl'></view>
					<view class="teacherHead" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view>
					<view class="teacherinfo">
						<view class="tName">
							<text>{{item.Name}}</text> <text class="Rname">{{item.Title}}</text>
						</view>
						<view class="Tin">{{item.Introduce}}</view>
					</view>
					<view class="teacherButton themeBGCC" @tap='toTrili' :data-teacherid="item.ID">
						立即约课
					</view>
				</view>
			</view>
			<view class="courseDetailBox">
				<view class="courseDetailTitle">课程简介</view>
				<view>
					<u-parse :content="course.GraphicDetails" :className='Chtml'/>
				</view>
			</view>
			<view class="courseDetailBox">
				<view class="courseDetailTitle">适合人群</view>
				<view class="courseDetailText">{{course.SuitsTheCrowd}}</view>
			</view>
			<view class="courseDetailBox">
				<view class="courseDetailTitle">注意事项</view>
				<view class="courseDetailText">{{course.MattersNeedingAttention}}</view>
			</view>
			<view class="courseDetailBox" style="padding-bottom: 60px;">
				<view class="courseDetailTitle">用户评价</view>
				<view class="commentItem" v-for="(item,index) in course.listEvaluateCourse" v-if='course.listEvaluateCourse.length != 0'>
					<view class="commentItempic" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}" v-if="item.isAnonymous == '1'"></view>
					<view class="commentItempic" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view>
					<view class="commentItemInfo" style="margin-left: 20px;">
						<view>
							<text v-if="item.isAnonymous == '1'">{{item.Name}}</text>
							<text v-else>匿名</text>
							<text style='margin-left: 20px;'>{{item.addTime}}</text>
						</view>
						<view>
							<text style="margin-right: 20px;">打分</text>
							<text class="iconfont iconpingjiaX" :class=" item.Evaluatestar >0 ? 'themeColor':'starHui'"></text>
							<text class="iconfont iconpingjiaX" :class=" item.Evaluatestar >1 ? 'themeColor':'starHui'"></text>
							<text class="iconfont iconpingjiaX" :class=" item.Evaluatestar >2 ? 'themeColor':'starHui'"></text>
							<text class="iconfont iconpingjiaX" :class=" item.Evaluatestar >3 ? 'themeColor':'starHui'"></text>
							<text class="iconfont iconpingjiaX" :class=" item.Evaluatestar >4 ? 'themeColor':'starHui'"></text>
						</view>
						<view>{{item.EvaluateInfo}}</view>
					</view>
				</view>
				<view class="kong noCard" v-if='course.listEvaluateCourse.length  == 0'>
					<view class="iconfont iconkong themeColor"></view>
					<view class="zanwu">暂无评价</view>
				</view>
			</view>
			<view class="fixBottom">
				<view class="fixButton themeBGCC" @tap='showPhone'>咨询课程</view>
			</view>
			<flow-button></flow-button>
			<uni-popup ref="popup2" type="dialog">
				<uni-popup-dialog title="即将拨打" @confirm="confirm" :content="phone"></uni-popup-dialog>
			</uni-popup>
		</view>
	</view>

	
</template>

<script>
	import {
		getCourseDetail
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	export default {
		components: {
			flowButton,
			uParse,
			uniPopup,
			uniPopupDialog
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				phone: uni.getStorageSync('Phone'),
				autoplay: true,
				indicatorDots: true,
				duration: 1000,
				interval: 3000,
				course: {
					listSlideshow: [],
					listTeacher: [],
					listEvaluateCourse: [],
					GraphicDetails: '',
					difficulty: '',
					CourseName: '',
					LevelName: '',
					GraphicDetails: ' ',
					SuitsTheCrowd: '',
					MattersNeedingAttention: '',
				},
				courseid: '',
				listStoresImg: []
			}
		},
		onLoad: function(e) {
			console.log(e);
			this.courseid = e.courseid;
			this.getCourseDetailF(e.courseid)
		},
		methods: {
			toTrili(e){
				var teacherid = e.currentTarget.dataset.teacherid;
				uni.navigateTo({
					url: '../teacherRili/teacherRili?teacherid='+teacherid+'&courseid='+this.courseid
				});
			},
			showPhone() {
				this.$refs.popup2.open();
			},
			getCourseDetailF(Tid) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getCourseDetail,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						CourseID: Tid,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.course = resData.data;

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
