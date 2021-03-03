<template>
	<view :class="themeName">
		<view class="whole">
			<view class="top">
				<view class="topSwiper">
					<swiper class="swiper" :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration"
					 style="border-radius: 20px;overflow: hidden;" :circular='true'>
						<swiper-item v-for="(item,index) in listStoresImg">
							<view class="lunboItem" :style="{backgroundImage:'url(' + item.ImgUrl + ')'}"></view>
							<!-- <image :src="item.ImgUrl"></image> -->
						</swiper-item>
					</swiper>
				</view>
			</view>
			<view class="fourBox">
				<view class="fourBoxItem" @tap='torili'>
					<view class="iconBox">
						<text class="iconfont iconyuyue themeColor"></text>
					</view>
					<view class="fourBoxItemText">
						约课
					</view>
				</view>
				<view class="fourBoxItem" @tap='toyuyue'>
					<view class="iconBox">
						<text class="iconfont iconyiyuyue themeColor"></text>
					</view>
					<view class="fourBoxItemText">
						我的预约
					</view>
				</view>
				<view class="fourBoxItem" @tap='toka'>
					<view class="iconBox">
						<text class="iconfont iconhuiyuka themeColor"></text>
					</view>
					<view class="fourBoxItemText">
						我的会员卡
					</view>
				</view>
				<view class="fourBoxItem" @tap='toxiaoxi'>
					<view class="iconBox">
						<text class="iconfont iconxiaoxi themeColor"></text>
					</view>
					<view class="fourBoxItemText">
						消息中心
					</view>
				</view>
			</view>
			<view class="tuankeBox">
				<view class="gonggao" @tap='toAn'>
					<text class="iconfont icongonggao themeColor" style="font-size: 32px;margin-top: -8px;"></text>
					<text class="GGtext">{{htAnnouncement.aTitle}}</text>
					<view class="GGtime">
						<text>{{htAnnouncement.addTime}}</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
			</view>
			<!-- 		<view class="tuankeBox">
				<view class="BoxTitle">
					<text class="BoxTitleText">约课</text>
					<view class="BoxMore">
						<text>课程日历</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
				<view class="riliList">
					<view class="riliItem" v-for="(item,index) in dateList" :class="activeIndex == index?'am-tabs-default-bar-tab-active':''"
					 @tap="getListRowClassTwoF(item.sendDate,index)">
						<view class="weekday">
							<text v-if="index == 0">今天</text>
							<text v-else>{{item.week}}</text>
						</view>
						<view class="dataday">{{item.listDate}}</view>
					</view>
				</view>
				<view class="riliKeList">
					<view class="riliKeListItem" v-for="(item,index) in RowClassTwo">
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
								{{item.ConsumptionOfClass}}课时
								<text class="riliKeListItemInfoClassPrice" v-if="item.preAboutType == '3' || item.preAboutType == '1'">￥{{item.singlePrice}}</text>
							</view>
							<view class="riliKeListItemInfoClassTeacher">
								{{item.Name}}
								<text class="can" v-if="item.showstate == 0">预约</text>
								<text class="cannot" v-if="item.showstate == 1">已结束</text>
							</view>
						</view>
					</view>
				</view>
			</view> -->
			<view class="tuankeBox" v-if="listCourse != undefined  && listCourse.length >0">
				<view class="BoxTitle">
					<text class="BoxTitleText">精品课程</text>
					<view class="BoxMore" @tap='tocourse'>
						<text>查看全部</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
				<view class="BoxList">
					<view class="BoxListItem" v-for="(item,index) in listCourse" @tap='toCourseDetail' :data-courseid="item.ID">
						<view class="boximg" :style="{backgroundImage:'url(' + item.HeadUrl + ')'}">

						</view>
						<view style='width: 120px;text-overflow: ellipsis;white-space: nowrap;overflow: hidden;'>{{item.CourseName}}</view>
					</view>
				</view>
			</view>
			<view class="tuankeBox" v-if="listCrad != undefined  && listCrad.length >0">
				<view class="BoxTitle">
					<text class="BoxTitleText">会员卡</text>
					<view class="BoxMore">
						<text>查看全部</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
				<view class="BoxList">
					<view class="BoxListItem" v-for="(item,index) in listCrad">
						<view class="boximg3" :style="{backgroundImage:'url(' + item.CradImgURL + ')'}" v-if="item.CradImgURL != ''">
							<view class="cardName">{{item.CardName}}</view>
							<view class="cardTime" v-if="item.CardType == 3 || item.CardType == 1 ">有效期：{{item.yxdays}}天</view>
							<view class="cardTime" v-else>有效期：不限期</view>
							<view class="cardPrice">￥{{item.SellingPrice}}</view>
						</view>
						<view class="boximg3" style="background-image: url(../../static/images/bluestyle/morenka.png);" v-else>
							<view class="cardName">{{item.CardName}}</view>
							<view class="cardTime" v-if="item.CardType == 3 || item.CardType == 1 ">有效期：{{item.yxdays}}天</view>
							<view class="cardTime" v-else>有效期：不限期</view>
							<view class="cardPrice">￥{{item.SellingPrice}}</view>
						</view>
					</view>
				</view>
			</view>
			<view class="tuankeBox" v-if="listTeacher != undefined  && listTeacher.length >0">
				<view class="BoxTitle">
					<text class="BoxTitleText">老师</text>
					<view class="BoxMore" @tap='toTeacher'>
						<text>查看全部</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
				<view class="BoxList">
					<view class="BoxListItem tacenter" v-for="(item,index) in listTeacher" @tap='toTeacherDetail' :data-teacherid="item.ID">
						<view class="boximg2" :style="{backgroundImage:'url(' + item.HeadUrl + ')'}" v-if="item.HeadUrl != ''"></view>
						<view class="boximg2" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view>
						<view>{{item.Name}}</view>
						<view>{{item.Title}}</view>
					</view>

				</view>
			</view>
			<view class="tuankeBox" style='margin-bottom: 20px;'>
				<view class="BoxTitle">
					<text class="BoxTitleText">门店介绍</text>
					<view class="BoxMore" @tap='toStoreInfo'>
						<text>查看详情</text>
						<text class="iconfont iconzuo"></text>
					</view>
				</view>
				<view class="adress">
					<text class="iconfont iconaddress"></text>
					<text class="addressText">{{htStores.Address}}</text>
					<view class="rightIcon">
						<text class="iconfont iconwx" style="margin-right: 10px;" @tap="showWX()"></text>
						<text class="iconfont icondianhua" @tap='showdianhua()'></text>
					</view>
				</view>
			</view>
			<view style="height: 50px;"></view>
			<bottom-Bar :index="indexNum"></bottom-Bar>
			<uni-popup ref="popup" type="center" :maskClick='false'>
				<view class="wxpop">
					<view style="position: relative;">
						{{htStores.StoresName}}
						<text style="font-size: 26px;position: absolute;top: -20px;right: 10px;" @tap='closeMa'>x</text>
					</view>
					<view>微信扫一扫</view>
					<image :src="htStores.KFQrCode"></image>
				</view>
			</uni-popup>
			<uni-popup ref="popup2" type="dialog">
				<uni-popup-dialog title="即将拨打" @confirm="confirm" :content="htStores.StoresPhone"></uni-popup-dialog>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	import {
		getHomeInfo,
		StudentLogin,
		getListRowClassTwo,
		getXCXOpenid
	} from '../../static/js/env.js'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import bottomBar from "@/components/bottomBar/bottomBar.vue";
	export default {
		components: {
			uniPopup,
			uniPopupDialog,
			bottomBar
		},
		data() {
			return {
				themeName: 'theme' + uni.getStorageSync('theme'),
				sId: '',
				indexNum: 1,
				autoplay: true,
				indicatorDots: true,
				duration: 300,
				interval: 3000,
				loading: true,
				indexNum: 1,
				listCourse: [],
				listCrad: [],
				listStoresImg: [],
				listTeacher: [],
				htStores: {
					StoresName: '',
					Address: '',
					KFQrCode: '',
					StoresPhone: '',
				},
				htAnnouncement: {
					aTitle: '',
					addTime: '',
				},
				dateList: [],
				RowClassTwo: [],
				activeIndex: 0,
				phone: 'tel:' + '18661026702',
				certNumber: ''
			}

		},
		onLoad(options) {
			// let _this = this;
			// function urlParams(scene) {
			// 	const str = decodeURIComponent(scene).replace('?', '&')
			// 	let strArr = str.split('&')
			// 	strArr = strArr.filter(item => item)
			// 	const result = {}
			// 	strArr.filter(item => {
			// 		const key = item.split('=')
			// 		result[key[0]] = key[1]
			// 	})
			// 	return result
			// }
			// //by zhengkai.blog.csdn.net
			// // scene 需要使用 decodeURIComponent 才能获取到生成二维码时传入的 scene
			// // const scene = decodeURIComponent(query.scene);
			// console.log(options);
			// if (Object.keys(options).length) {
			// 	console.log(1);
			// 	console.log(options);
			// 	console.log(options.StoresID);
			// 	let invite = '';
			// 	let scene = '';
			// 	if(options.StoresID){
			// 		invite = options.StoresID;
			// 		console.log(invite);
			// 	}
			// 	if(options.scene){
			// 		scene = options.scene;
			// 		console.log(scene);
			// 	}
			// 	// const {invite,scene} = options;
			// 	if (scene) { /* 扫码进来 */
			// 	    console.log(2);
			// 		const sceneObj = urlParams(scene)
			// 		console.log(sceneObj);
			// 		if (sceneObj.StoresID) {
			// 			console.log(sceneObj);
			// 			_this.sId = sceneObj.StoresID;
			// 			console.log(_this.sId);
			// 			// uni.setStorageSync('StoresID', sceneObj.StoresID)
			// 		}
			// 	}
			// 	/* 正常情况，包括H5端、APP端 */
			// 	if (invite) {
			// 		console.log(3);
			// 		console.log(invite);
			// 		_this.sId = invite;
			// 		console.log(_this.sId);
			// 		// uni.setStorageSync('StoresID', invite.StoresID)
			// 	}
			// }

			// if(this.sId){
			// 	uni.setStorageSync('StoresID', this.sId)
			// }
			if (uni.getStorageSync('openid')) {
				this.StudentLoginF();
			} else {
				this.login();
			}
			// console.log(query)
			// console.log(query.StoresID)
			// if (query.StoresID) {
			// 	uni.setStorageSync('StoresID', query.StoresID)

			// }

			// console.log(scene);
			//类型为string，所以只能判断不叫"undefined"
			//console.log(typeof(scene));
			// if(scene!="undefined"&&scene.length>4){
			// 	console.log(scene);
			// 	uni.showModal({
			// 		content: '证书编码:'+scene+'，点击查询查看详情',
			// 		showCancel: false
			// 	});
			// 	this.certNumber=scene;
			// }else{
			// 	console.log("没有启动参数");
			// }
		},
		onShow() {

		},
		created() {

		},
		methods: {
			torili() {
				uni.navigateTo({
					url: '../rili/rili'
				});
			},
			toyuyue() {
				if (uni.getStorageSync('StudentID') == '0') {
					uni.reLaunch({
						url: '../login/login'
					});
				} else {
					uni.navigateTo({
						url: '../yuyue/yuyue'
					});
				}

			},
			toka() {
				if (uni.getStorageSync('StudentID') == '0') {
					uni.reLaunch({
						url: '../login/login'
					});
				} else {
					uni.navigateTo({
						url: '../mycard/mycard'
					});
				}
			},
			toxiaoxi() {
				if (uni.getStorageSync('StudentID') == '0') {
					uni.reLaunch({
						url: '../login/login'
					});
				} else {
					uni.navigateTo({
						url: '../notice/notice'
					});
				}

			},

			login() {
				let _this = this;
				// 1.wx获取登录用户code
				uni.login({
					provider: 'weixin',
					success: function(loginRes) {
						console.log(loginRes);
						let code = loginRes.code;
						// if (!_this.isCanUse) {
						//     //非第一次授权获取用户信息
						//     uni.getUserInfo({
						//         provider: 'weixin',
						//         success: function(infoRes) { 　　　　　　　　　　　　　　　　　　　　　　//获取用户信息后向调用信息更新方法
						//             let nickName = infoRes.userInfo.nickName; //昵称
						//             let avatarUrl = infoRes.userInfo.avatarUrl; //头像
						//                 _this.updateUserInfo();//调用更新信息方法
						//         }
						//     });
						// }

						//2.将用户登录code传递到后台置换用户SessionKey、OpenId等信息
						uni.request({
							url: getXCXOpenid,
							data: {
								code: code,
								StoresID: uni.getStorageSync('StoresID')
							},
							method: 'POST',
							success: (res) => {
								let resData = res.data;
								console.log(resData)
								//openId、或SessionKdy存储//隐藏loading
								uni.setStorageSync('openid', resData.openid)
								if (resData.templateType == '1') {
									uni.setStorageSync('theme', 'blue');
									uni.setBackgroundColor({
										backgroundColor: '#ffffff',
									});
								} else if (resData.templateType == '2') {
									uni.setStorageSync('theme', 'pink');
									uni.setBackgroundColor({
										backgroundColor: '#ffffff',
									});
								} else if (resData.templateType == '3') {
									uni.setStorageSync('theme', 'pink');
									uni.setBackgroundColor({
										backgroundColor: '#ffffff',
									});
								} else {
									uni.setStorageSync('theme', 'green');
									uni.setBackgroundColor({
										backgroundColor: '#ffffff',
									});

								}
								if (resData.IsxcxPublic == 1 && resData.tourl != '') {
									uni.request({
										url: getXCXOpenid,
										method: 'get',
										data: {
											code: code,
											StoresID: uni.getStorageSync('StoresID')
										},
										success: res => {
											let resData = res.data;
											if (resData.orsuccess == "1") {

											} else {

											}

										},
										fail: () => {},
										complete: () => {}
									});
								}
								_this.StudentLoginF();

							}
						});
					},
				});
			},

			toCourseDetail(e) {
				var courseid = e.currentTarget.dataset.courseid;
				uni.navigateTo({
					url: '../courseDetail/courseDetail?courseid=' + courseid
				});
			},
			toTeacherDetail(e) {
				var teacherid = e.currentTarget.dataset.teacherid;
				uni.navigateTo({
					url: '../teacherDetail/teacherDetail?teacherid=' + teacherid
				});
			},
			toTeacher() {
				uni.navigateTo({
					url: '../teacherList/teacherList'
				});
			},
			toStoreInfo() {
				uni.navigateTo({
					url: '../storeInfo/storeInfo'
				});
			},
			toAn() {
				uni.navigateTo({
					url: '../announcements/announcements'
				});
			},
			tocourse() {
				uni.navigateTo({
					url: '../courseList/courseList'
				});
			},
			closeMa() {
				this.$refs.popup.close();
			},
			showWX() {
				this.$refs.popup.open();
			},
			showdianhua() {
				this.$refs.popup2.open();
			},
			confirm() {
				uni.makePhoneCall({
					phoneNumber: this.htStores.StoresPhone,
					success: (res) => {
						console.log('调用成功!')
					},
					fail: (res) => {
						console.log('调用失败!')
					},
				});
			},
			formatDate(nowDate) {
				let year = nowDate.getFullYear();
				let month = (nowDate.getMonth() + 1);
				let date = nowDate.getDate();
				let week = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'][nowDate.getDay()];
				let _list = {
					week,
					showDate: date,
					listDate: `${month}/${date}`,
					sendDate: `${year}-${month}-${date}`,
					now: nowDate.toLocaleDateString() === new Date().toLocaleDateString(),
					tomorrow: false
				}
				// if(_list.now == true){

				// }
				return _list
			},
			addDate(date, n) {
				date.setDate(date.getDate() + n);
				return date;
			},
			setDate(date) {
				// let week = date.getDay();
				// date = this.addDate(date, week * 1);
				this.currentFirstDate = new Date(date);
				let list = []
				for (let i = 0; i < 14; i++) {
					list.push(this.formatDate(i === 0 ? this.addDate(date, 0) : this.addDate(date, 1)));
				}
				for (let i = 0; i < list.length; i++) {
					if (list[i].now == true) {
						list[i + 1].tomorrow = true;
						break
					}
				}
				this.dateList = list;
				this.getListRowClassTwoF(list[0].sendDate, 0)
			},
			StudentLoginF() {

				uni.showLoading({
					title: "加载中...",
					mask: true
				})
				uni.request({
					url: StudentLogin,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						openid: uni.getStorageSync('openid'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.setStorageSync('token', resData.token);
							uni.setStorageSync('StudentID', resData.StudentID);
							if (resData.data.openid) {
								uni.setStorageSync('openid', resData.data.openid);
								uni.setStorageSync('wx_name', resData.data.wx_name);
								uni.setStorageSync('wx_topimg', resData.data.wx_topimg);
							}

							let date = new Date();
							this.setDate(date);
							this.getHomeInfoF();
						} else {
							uni.showToast({
								title: '登陆失败',
								icon: 'none',
								duration: 2000
							})
						}

					},
					fail: () => {},
					complete: () => {}
				});

			},
			getListRowClassTwoF(data, index) {
				this.activeIndex = index;
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListRowClassTwo,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						dateTime: data,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.RowClassTwo = resData.data;
							uni.hideLoading();
						} else {

						}

					},
					fail: () => {},
					complete: () => {}
				});
			},
			getHomeInfoF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getHomeInfo,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.listCourse = resData.listCourse;
							//this.listCrad = resData.listCrad;
							if (resData.listStoresImg != []) {
								this.listStoresImg = resData.listStoresImg;
							} else {
								let item = {
									ID: '999',
									ImgUrl: '../../static/images/bluestyle/lunbomoren.png',
									addTime: '2020/12/10 15:31:01',
								}
								this.listStoresImg.push(item);
							}
							this.listTeacher = resData.listTeacher;
							this.htStores = resData.htStores;
							this.htAnnouncement = resData.htAnnouncement;
							uni.setStorageSync('Phone', resData.htStores.StoresPhone);
							uni.setStorageSync('KFQrCode', resData.htStores.KFQrCode);
							uni.setStorageSync('Address', resData.htStores.Address);
							uni.setStorageSync('storeName', resData.htStores.StoresName);
							uni.setNavigationBarTitle({
								title: resData.htStores.StoresName
							})

							uni.hideLoading();
						} else {

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
