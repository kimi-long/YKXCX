<template>
	<view :class="themeName">
		<view class="pageyuyue">
			<list>
				<view class='content' v-if="listItem.length > 0">
					<view class="yuyueListItem" v-for="(item,index) in listItem" :key='index' @tap='toLesson' :data-statec="item.state"
					 :data-RowClassID="item.ID" :data-PreAboutID="item.PreAboutID">
						<view class="riliKeListItemImg" :style="{backgroundImage: 'url(' + item.HeadUrl + ')'}">
							<!-- 							<text class="riliKeListItemImgText" v-if="currentTab == 0 ||currentTab == 3 ">已预约</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 1">已完成</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 0">学员取消</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 1">预约</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 2">签到</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 3">未到</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 4">机构取消</text>
							<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 5">请假</text> -->
						</view>

						<view class="riliKeListItemInfo">
							<view class="firstLine">
								<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '0'">班课</text>
								<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '1'">私教</text>
								<text class="riliKeListItemInfoName">{{item.CourseName}}</text>
							</view>
							<view class="riliKeListItemInfoTime">
								{{item.SKtime}} {{item.startTime}}
							</view>
							<view class="riliKeListItemInfoClassTime">
								<text class="riliKeListItemInfoClassTime" v-if='item.ConsumptionOfClass'>{{item.ConsumptionOfClass}}课时</text>
								<text class="riliKeListItemInfoClassPrice" v-if='Math.round(item.singlePrice) != 0'>￥{{item.singlePrice}}</text>
							</view>
							<view class="riliKeListItemInfoClassTeacher">
								{{item.Name}}
								<text class="can" @tap='showqiandao(item.PreAboutID)' v-if='item.state == 1'>签到</text>
								<text class="cannot" v-else>已签到</text>
							</view>
						</view>
					</view>
					<view class="mybnWMth">已到底部</view>


				</view>
			</list>
			<view class="kong noCard" v-if='listItem.length == 0' style="padding: 100px 0;">
				<view class="iconfont iconkong themeColor"></view>
				<view class="zanwu">暂无您需要签到的课程！</view>
			</view>
			<view class="qiehuan themeColor" v-if='ZHList.length > 1' @tap='showqiehuan'>切换账号</view>
			<view style="width: 100%;height: 50upx;opacity:0;">底部占位盒子</view>
		</view>
		<uni-popup ref="popup" type="dialog">
			<uni-popup-dialog title="通知" @confirm="UpdStuPreAboutState" content="是否确认签到?"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="popup2" type="center" :maskClick='false'>
			<view class="popBox">
				<view class="popTitle">切换账号</view>
					<view class="popList">
						<radio-group @change="radioChange">
							<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in ZHList" :key="item.ID">
								<view>
									<radio :value="item.ID" :checked="index === current" />
								</view>
								<view class="popListItemImg" :style="{backgroundImage: 'url('+item.HeadUrl+')'}" v-if='item.HeadUrl'></view>
								<view class="popListItemImg" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view>
								<view style="font-size: 16px;" class="name">{{item.Name}}</view>
							</label>
						</radio-group>
					</view>
				<view class="popButtonBox">
					<view class="popButton" style="border-right: 1px solid #EFEFEF;" @tap='quxiao'>取消</view>
					<view class="popButton themeColor" @tap='queding'>确定</view>
				</view>
			</view>
		</uni-popup>
	</view>

</template>

<script>
	import {
		getXCXRowClass,
		UpdStuPreAboutState
	} from '../../static/js/env.js'
	import flowButton from '../../components/flowButton/flowButton.vue'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	export default {
		components: {
			flowButton,
			uniPopup,
			uniPopupDialog
		},
		data() {
			return {
				checkId: "",
				listItem: [],
				ZHList: [],
				current: 0,
				themeName: 'theme' + uni.getStorageSync('theme'),
			};
		},
		created() {
			this.getXCXRowClassF();
		},
		methods: {
			radioChange: function(evt) {
				console.log(evt)
				for (let i = 0; i < this.ZHList.length; i++) {
					if (this.ZHList[i].ID === evt.target.value) {
						this.current = i;

					} else {

					}
				}
			},

			quxiao() {
				this.$refs.popup2.close()
			},
			showqiehuan() {
				this.current = 0;
				this.$refs.popup2.open()
			},
			queding() {
				// console.log(this.ZHList);
				// let newStu = '';
				// this.ZHList.forEach((item,index)=>{
				// 	if(item.value){
				// 		newStu = item.ID;
				// 	}
				// })
				// console.log(newStu)
				uni.setStorageSync('StudentID', this.ZHList[this.current].ID);
				this.getXCXRowClassF();
				this.$refs.popup2.close();
			},

			showqiandao(PreAboutID) {
				this.$refs.popup.open();
				this.PreAboutID = PreAboutID;
			},
			toCourseDetail(e) {
				var courseid = e.currentTarget.dataset.courseid;
				uni.navigateTo({
					url: '../courseDetail/courseDetail?courseid=' + courseid
				});
			},

			UpdStuPreAboutState() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: UpdStuPreAboutState,
					method: 'POST',
					data: {
						PreAboutID: this.PreAboutID,
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: "签到成功",
								duration: 3000,
								icon: 'success'
							})
							this.$refs.popup.close();
							uni.hideLoading();
							this.getXCXRowClassF();
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
			getXCXRowClassF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getXCXRowClass,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						openid: uni.getStorageSync('openid'),
						stuID: uni.getStorageSync('StudentID'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.listItem = resData.data;
							this.ZHList = resData.listMembers;
							this.ZHList.forEach((item, index) => {
								if (index == 0) {
									item.value = true;
								} else {
									item.value = false;
								}
							})
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
	.qiehuan {
		font-size: 16px;
		margin-top: 20px;
		height: 50px;
		line-height: 50px;
		width: 100%;
		text-align: center;
		background-color: rgb(35, 35, 35);
	}
</style>
