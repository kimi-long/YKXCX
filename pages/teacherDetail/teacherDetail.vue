<template>
	<view :class="themeName">
		<view class="teacherDetail">
			<view class="teacherTop">
				<view class="imgBG" :style="{backgroundImage: 'url(' + teacherInfo.HeadUrl + ')'}" v-if="teacherInfo.HeadUrl !=''"></view>
				<view class="imgBG" style='background-image: url(../../static/images/bluestyle/moren.png);' v-else></view>
				<view class="teacherCard">
					<view class="headImg" :style="{backgroundImage: 'url(' + teacherInfo.HeadUrl + ')'}" v-if="teacherInfo.HeadUrl !=''"></view>
					<view class="headImg" style='background-image: url(../../static/images/bluestyle/moren.png);' v-else></view>
					<view class="teacherName">{{teacherInfo.Name}}</view>
					<view class="teacherTitle">{{teacherInfo.Title}}</view>
				</view>
				<view class="teacherIntro">个人介绍：{{teacherInfo.Introduce}}</view>
			</view>
			<view class="teacherMid">
				<view class="teacherMidTitle">主要授课</view>
				<view class="teacherMidItem" v-for="(item,index) in teacherInfo.listCourse">
					<view class="teacherMidItemImg" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}"></view>
					<view class="teacherMidItemInfo">
						<view class="teacherMidItemInfoName">{{item.CourseName}}</view>
						<view class="teacherMidItemInfoNum">{{item.yyCount}}人预约过
						<view class="teacherButton themeBGCC" @tap='toTrili' :data-teacherid="item.TeacherIDs" :data-courseid="item.ID">
							立即约课
						</view>
						</view>
					</view>
		
				</view>
			</view>
			<view class="teacherCom">
				<view class="teacherComTitle">用户评价</view>
				<view class="commentItem" v-for="(item,index) in teacherInfo.listEvaluateTeacher" v-if='teacherInfo.listEvaluateTeacher.length != 0'>
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
				<view class="kong noCard" v-if='teacherInfo.listEvaluateTeacher.length == 0'>
					<view class="iconfont iconkong themeColor"></view>
					<view class="zanwu">暂无评价</view>
				</view>
			</view>
			<view class="fixBottom">
				<view class="fixButton themeBGCC" @tap='showComment'>评价老师</view>
			</view>
			<flow-button></flow-button>
			<uni-popup ref="popup" type="bottom" :maskClick='false'>
				<view class="popupinner" v-if='showInner'>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle">是否匿名</view>
						<view style="position: relative;top: -4px;">
							<switch :checked='isNoname' style="transform:scale(0.7)" :color="themeColor" @change="changeSw"/>
						</view>
					</view>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle">评价类别</view>
						<view>
							<radio-group @change="radioChange">
								<label class="radio" v-for="(item,index) in typeList" :key="item.value">
									<radio :value="item.value" :checked="index +1 === current" :color="themeColor"/>
									<text class="radioText">{{item.name}}</text>
								</label>
							</radio-group>
						</view>
					</view>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle"  style="line-height: 46px;">星级打分</view>
						<view>
							<text class="iconfont iconpingjiaX" :class=" commentStar >0 ? 'themeColor':'starHui'" @tap='changeStar(1)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >1 ? 'themeColor':'starHui'" @tap='changeStar(2)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >2 ? 'themeColor':'starHui'" @tap='changeStar(3)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >3 ? 'themeColor':'starHui'" @tap='changeStar(4)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >4 ? 'themeColor':'starHui'" @tap='changeStar(5)'></text>
						</view>
					</view>
					<view class="popupinnerLineTextarea">
						<textarea  placeholder="请输入评价" :value='commentText' maxlength='100' @input='changetext'/>
					</view>
					<view class="checkButtonBox">
						<view class="checkButtonBoxItem themeBGCC" @tap='closeComment'>取消</view>
						<view class="checkButtonBoxItem themeBGCC" @tap='addEvaluateTeacherF'>确认</view>
					</view>
				</view>
		
			</uni-popup>
		</view>
	</view>

</template>

<script>
	import {
		getTeacherDetail,
		addEvaluateTeacher
	} from '../../static/js/env.js'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton,
			uniPopup
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				showInner:false,
				teacherID:'',
				themeColor:uni.getStorageSync('themeColor'),
				current:1,
				commentStar:0,
				commentText:'',
				typeList: [{
						name: '好评',
						value: '1',
					},
					{
						name: '普通',
						value: '2',
					},
					{
						name: '差评',
						value: '3',
					}
				],
				isNoname: false,
				teacherInfo: {
					HeadUrl: "",
					Name: "",
					Title: "",
					Introduce: "",
					listCourse: [],
					listEvaluateTeacher: [],
				},
			};
		},
		onLoad: function(e) {
			console.log(e);
			this.getTeacherDetailF(e.teacherid)
			this.teacherID = e.teacherid;
		},
		methods: {
			toTrili(e){
				var teacherid = e.currentTarget.dataset.teacherid;
				var courseid = e.currentTarget.dataset.courseid;
				uni.navigateTo({
					url: '../teacherRili/teacherRili?teacherid='+teacherid+'&courseid='+courseid
				});
			},
			changeSw(e){
				this.isNoname = e.target.value;
			},
			changetext(e){
				console.log(e);
				this.commentText = e.detail.value;
			},
			closeComment(){
				this.showInner = false;
				this.$refs.popup.close();
			},
			changeStar(num){
				this.commentStar = num;
			},
			radioChange(evt) {
				for (let i = 0; i < this.typeList.length; i++) {
					if (this.typeList[i].value === evt.target.value) {
						this.current = i+1;
						break;
					}
				}
			},
			showComment() {
				this.isNoname =false;
				this.current = 1;
				this.commentStar = 0;
				this.commentText = '';
				this.showInner = true;
				this.$refs.popup.open();
			},
			
			addEvaluateTeacherF(Tid) {
				console.log(this.commentText);
				console.log(this.current);
                let aa= '2';
                if(!this.isNoname){
                    aa = '1'
                }
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: addEvaluateTeacher,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						StudentID: uni.getStorageSync('StudentID'),
						TeacherID: this.teacherID,
						EvaluateInfo: this.commentText,
						EvaluateType:this.current,
						Evaluatestar:this.commentStar,
						token: uni.getStorageSync('token'),
						isAnonymous:aa,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: '评价成功',
								icon: 'none',
								duration: 2000
							})
							this.$refs.popup.close();
							this.getTeacherDetailF(this.teacherID);
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
			getTeacherDetailF(Tid) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getTeacherDetail,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						TeacherID: Tid,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.teacherInfo = resData.data;
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
