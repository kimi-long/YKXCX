<template>
	<view :class="themeName">
		<view class="pagekechengDetail">
			<view class="courseDetailInfo">
				<view class="coureseListItemImg" :style="{backgroundImage: 'url(' + course.CourseHeadUrl + ')'}"></view>
				<view class="coureseListItemInfo">
					<view class="coureseListItemInfoNameAll">
						<text v-if="course.RowClassType=='0'" class="themeTag">班课</text>
						<text v-if="course.RowClassType=='1'" class="themeTag">私教</text>
						<text class="coureseListItemInfoName">{{course.CourseName}}</text>
					</view>
					<view class="coureseListItemInfosuitPeople">
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >0 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >1 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >2 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >3 ? 'themeColor':'starHui'"></text>
						<text class="iconfont iconpingjiaX" :class=" course.difficulty >4 ? 'themeColor':'starHui'"></text>
					</view>
					<view class="coureseListItemInfolevel">{{course.LoginName}}</view>
				</view>
			</view>
			<view class="bar" v-if="course.isbuyCrad == '1' && type == '5' ">
				<uni-notice-bar single="true" showClose='true' :text="course.buyinfo"></uni-notice-bar>
			</view>

			<view class="courseDetailTime">
				<view class="courseDetailTimeTitle">上课时间</view>
				<view class="courseDetailTimeText">{{course.SKtime}} {{course.startTime}}</view>
			</view>
			<view class="courseDetailTime">
				<view class="courseDetailTimeTitle">上课人数</view>
				<view class="courseDetailTimeText">
					<text v-if="course.limitCount != 0">{{course.yallcount}}/{{course.limitCount}}人</text>
					<text v-else>不限人数</text>
				</view>
			</view>
			<view class="courseDetailNum" v-if="course.listMUrl != undefined && course.listMUrl.length >0">
				<view class="courseDetailNumTitle">已约会员</view>
				<view class="courseDetailNumImg">
					<view class="courseDetailNumImgItem" v-for="(item,index) in course.listMUrl">
						<view class="iconItem" :style="{backgroundImage: 'url(' + item.HeadUrl + ')','background-size': '100% 100%','background-repeat':'no-repeat'}"
						 v-if="item.HeadUrl != ''"></view>
						<view class="iconItem" v-else style="background-image: url(../../static/images/bluestyle/moren.png) ;background-size: cover;"></view>
					</view>
				</view>
			</view>
			<view class="fixBottom">
				<view class="fixButton themeBGCC" v-if="type == '5' && course.isbuyCrad == '0'" @tap='getTime'>预约课程</view>
				<view class="fixButton themeBGCC" v-if="type == '5' && course.isbuyCrad == '1'" @tap='showPhone'>立即咨询</view>
				<view class="fixButton themeBGCC" v-if="type == '1'" @tap='cannelYuyue'>取消预约</view>
				<view class="fixButton themeBGCC" v-if="type == '2'" @tap='comment'>评价课程</view>
				<view class="fixButton themeBGCC" v-if="type == '3' && (state == 0 || state == 4)" @tap='getTime'>重新预约</view>
				<view class="fixButton themeBGCC" v-if="type == '4' && course.isyueke == 1" @tap='getTime'>预约课程</view>
				<view class="fixButton themeBGCCL" v-if="type == '4' && course.isyueke != 1">您已预约</view>

			</view>
			<flow-button></flow-button>
			<uni-popup ref="popup2" type="dialog">
				<uni-popup-dialog title="即将拨打" @confirm="confirm" :content="phone"></uni-popup-dialog>
			</uni-popup>
			<uni-popup ref="popup" type="dialog">
				<uni-popup-dialog title="通知" @confirm="confirmagagin" content="是否确认重新预约?"></uni-popup-dialog>
			</uni-popup>
			<uni-popup ref="popup3" type="dialog">
				<uni-popup-dialog title="通知" @confirm="confirmonce" content="是否确认预约课程?"></uni-popup-dialog>
			</uni-popup>
			<uni-popup ref="popup4" type="dialog">
				<uni-popup-dialog title="通知" @confirm="confirquxiao" content="是否确认取消预约?"></uni-popup-dialog>
			</uni-popup>
			<uni-popup ref="popup5" type="bottom" :maskClick='false'>
				<view class="popupinner" v-if='showInner'>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle">是否匿名</view>
						<view style="position: relative;top: -4px;">
							<switch :checked='isNoname' style="transform:scale(0.7)" :color="themeColor" @change="changeSw" />
						</view>
					</view>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle">评价类别</view>
						<view>
							<radio-group @change="radioChange">
								<label class="radio" v-for="(item,index) in typeList" :key="item.value">
									<radio :value="item.value" :checked="index +1 === current" :color="themeColor" />
									<text class="radioText">{{item.name}}</text>
								</label>
							</radio-group>
						</view>
					</view>
					<view class="popupinnerLine">
						<view class="popupinnerLinetitle" style="line-height: 46px;">星级打分</view>
						<view>
							<text class="iconfont iconpingjiaX" :class=" commentStar >0 ? 'themeColor':'starHui'" @tap='changeStar(1)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >1 ? 'themeColor':'starHui'" @tap='changeStar(2)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >2 ? 'themeColor':'starHui'" @tap='changeStar(3)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >3 ? 'themeColor':'starHui'" @tap='changeStar(4)'></text>
							<text class="iconfont iconpingjiaX" :class=" commentStar >4 ? 'themeColor':'starHui'" @tap='changeStar(5)'></text>
						</view>
					</view>
					<view class="popupinnerLineTextarea">
						<textarea placeholder="请输入评价" :value='commentText' maxlength='100' @input='changetext' />
						</view>
					<view class="checkButtonBox">
						<view class="checkButtonBoxItem themeBGCC" @tap='closeComment'>取消</view>
						<view class="checkButtonBoxItem themeBGCC" @tap='addEvaluateCourseF'>确认</view>
					</view>
				</view>
			
			</uni-popup>
			
			<uni-popup ref="popuptime" type="center" :maskClick='false'>
				<view class="popBox" v-if='timeshow'>
					<view class="popTitle">选择时间</view>
					<scroll-view style="height: 230px;" scroll-y="true" show-scrollbar="true">
                       <view class="popList" style="overflow-y: ;">
                       	<radio-group @change="radioChange2">
                       		<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in TimeList" :key="item.value">
                       			<view>
                       				<radio :value="item.ID" :checked="index === currentTime" />
                       			</view>
                       			<view style="font-size: 16px;" class="themeColor">{{item.startTime}}—{{item.endTime}}</view>
                       		</label>
                       	</radio-group>
                       </view>
					</scroll-view>

					<view class="popButtonBox">
						<view class="popButton" style="border-right: 1px solid #EFEFEF;" @tap='quxiao'>取消</view>
						<view class="popButton themeColor" @tap='queding'>确定</view>
					</view>
				</view>
			</uni-popup>
		</view>
	</view>

</template>

<script>
	import {
		getRowClassDetail,
		getListTeacherRowClass,
		getstuListRowClassTime,
		StuAddPreAbout,
		StuUpdPreAboutState,
		addEvaluateCourse
	} from '../../static/js/env.js'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import flowButton from '../../components/flowButton/flowButton.vue'
	export default {
		components: {
			flowButton,
			uniPopup,
			uniPopupDialog
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				showInner:false,
				timeshow:false,
				themeColor:uni.getStorageSync('themeColor'),
				current:1,
				currentTime:0,
				currentTimeStr:'',
				commentStar:0,
				commentText:'',
				course: {
					CourseHeadUrl:"",
					RowClassType:"",
					difficulty:"",
					LoginName:"",
					buyinfo:"",
					SKtime:"",
					startTime:"",
					yallcount:"",
					limitCount:"",
					listMUrl:[],
					isbuyCrad:"",

				},
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
				PreAboutID: '',
				RowClassID: '',
				type: '',
				state: '',
				phone: uni.getStorageSync('Phone'),
				changeradio:'',
				TimeList:[]
				
			};
		},
		onLoad: function(e) {
			console.log(e);
			this.PreAboutID = e.PreAboutID;
			this.RowClassID = e.RowClassID;
			this.type = e.type;
			this.state = e.state;
			this.getRowClassDetailF(e.teacherid);
			// this.getstuListRowClassTime();
		},
		methods: {
			quxiao(){
				this.$refs.popuptime.close();
			},
			queding(){
				this.$refs.popuptime.close();
				if (this.type == '3') {
					this.againYuyue();
				} else if (this.type == '4') {
					this.onceYuyue();
				} else if (this.type == '5') {
					this.onceYuyue();
				}
			},
			radioChange: function(evt) {
				for (let i = 0; i < this.typeList.length; i++) {
					if (this.typeList[i].value === evt.target.value) {
						this.current = i+1;
						break;
					}
				}
			},
			radioChange2: function(evt) {
				for (let i = 0; i < this.TimeList.length; i++) {
					if (this.TimeList[i].ID === evt.target.value) {
						this.currentTime = i;
						this.currentTimeStr = this.TimeList[i].ID;
						break;
					}
				}
			},
			changeSw(e){
				this.isNoname = e.target.value;
			},
			changetext(e){
				console.log(e);
				this.commentText = e.detail.value;
			},
			closeComment(){
				this.$refs.popup5.close();
				this.showInner = false;
			},
			changeStar(num){
				this.commentStar = num;
			},
			confirmagagin(){
				this.StuAddPreAbout();
			},
			confirmonce(){
				this.StuAddPreAboutOnce();
			},
			confirquxiao(){
				this.StuUpdPreAboutState();
			},
			getTime() {
				if (this.course.RowClassType == '1') {
					this.getstuListRowClassTime()
				} else {
					if (this.type == '3') {
						this.againYuyue();
					} else if (this.type == '4') {
						this.onceYuyue();
					} else if (this.type == '5') {
						this.onceYuyue();
					}
				}
			},
			againYuyue(){
				this.$refs.popup.open();
			},
			onceYuyue(){
				this.$refs.popup3.open();
			},
			showPhone() {
				this.$refs.popup2.open();
			},
			cannelYuyue(){
				this.$refs.popup4.open();
			},
			comment(){
				this.isNoname =false;
				this.current = 1;
				this.commentStar = 0;
				this.commentText = '';
				this.showInner = true;
				this.$refs.popup5.open();
			},
			
			confirm() {
				uni.makePhoneCall({
					phoneNumber: this.phone,
					success: (res) => {
						console.log('调用成功!')
					},
					fail: (res) => {
						console.log('调用失败!')
					},
				});
			},
			addEvaluateCourseF(Tid) {
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
					url: addEvaluateCourse,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						StudentID: uni.getStorageSync('StudentID'),
						RowClassID: this.RowClassID,
						CourseID: this.course.CourseID,
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
							this.$refs.popup4.close();
							uni.navigateBack();
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
			StuUpdPreAboutState() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: StuUpdPreAboutState,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						PreAboutID: this.PreAboutID,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: "取消成功",
								icon: 'success'
							})
							uni.navigateTo({
								url: '../yuyue/yuyue'
							});
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
			StuAddPreAboutOnce() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: StuAddPreAbout,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						StudentID: uni.getStorageSync('StudentID'),
						token: uni.getStorageSync('token'),
						RowClassID: this.RowClassID,
						limitCount: this.course.limitCount,
						RowClassType: this.course.RowClassType,
						RowClassTimeID: this.currentTimeStr,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: "预约成功",
								icon: 'success'
							})
							uni.navigateTo({
								url: '../yuyue/yuyue'
							});
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
			getstuListRowClassTime() {
				this.timeshow = false;
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getstuListRowClassTime,
					method: 'POST',
					data: {
						token: uni.getStorageSync('token'),
						RowClassID: this.RowClassID,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.TimeList = resData.data;
							this.currentTime = 0 ;
							this.currentTimeStr  = resData.data[0].ID ;
							this.timeshow = true;
							this.$refs.popuptime.open();
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
			StuAddPreAbout() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: StuAddPreAbout,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						StudentID: uni.getStorageSync('StudentID'),
						token: uni.getStorageSync('token'),
						RowClassID: this.RowClassID,
						PreAboutID: this.PreAboutID,
						limitCount: this.course.limitCount,
						RowClassType: this.course.RowClassType,
						RowClassTimeID: this.currentTimeStr,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: "预约成功",
								icon: 'success'
							})
							uni.navigateTo({
								url: '../yuyue/yuyue'
							});
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
			getRowClassDetailF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getRowClassDetail,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						StudentID: uni.getStorageSync('StudentID'),
						token: uni.getStorageSync('token'),
						RowClassID: this.RowClassID,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.course = resData.data;
							uni.hideLoading();
						} else {
							if(resData.Msg !='用户不存在' ){
							uni.showToast({
								title: resData.Msg,
								icon: 'none',
								duration: 2000
							})
							}else {
							      uni.reLaunch({
							      	url: '../login/login'
							      });
						    }
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
		},
	}
</script>

<style lang="less">
    // /deep/.uni-scroll-view::-webkit-scrollbar {
    // /* 隐藏滚动条，但依旧具备可以滚动的功能 */
    // display: block !important;
    // }
	    ::-webkit-scrollbar
	    {
	        width: 16upx!important;
	        height: 16upx!important;
	        background-color: #D3D3D3;
	    }
	      
	    /*定义滚动条轨道 内阴影+圆角*/
	    ::-webkit-scrollbar-track
	    {
	        // -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
	        border-radius: 10px;
	        background-color: #fff;
	    }
	      
	    /*定义滑块 内阴影+圆角*/
	    ::-webkit-scrollbar-thumb
	    {
	        border-radius: 10px;
	        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
	        background-color: #D3D3D3;
	    }
</style>
