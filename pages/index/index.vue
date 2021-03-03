<template>

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
				themeName:'theme'+uni.getStorageSync('theme'),
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
				certNumber: '',
				showview: '',
			}

		},
		onLoad(options) {

			let _this = this;
			function urlParams(scene) {
				const str = decodeURIComponent(scene).replace('?', '&')
				let strArr = str.split('&')
				strArr = strArr.filter(item => item)
				const result = {}
				strArr.filter(item => {
					const key = item.split('=')
					result[key[0]] = key[1]
				})
				return result
			}
			//by zhengkai.blog.csdn.net
			// scene 需要使用 decodeURIComponent 才能获取到生成二维码时传入的 scene
			// const scene = decodeURIComponent(query.scene);
			console.log(options);
			if (Object.keys(options).length) {
				console.log(1);
				console.log(options);
				console.log(options.StoresID);
				let invite = '';
				let scene = '';
				if(options.StoresID){
					invite = options.StoresID;
					console.log(invite);
				}
				if(options.scene){
					scene = options.scene;
					console.log(scene);
				}
				// const {invite,scene} = options;
				if (scene) { /* 扫码进来 */
				    console.log(2);
					const sceneObj = urlParams(scene)
					console.log(sceneObj);
					if (sceneObj.StoresID) {
						console.log(sceneObj);
						_this.sId = sceneObj.StoresID;
						console.log(_this.sId);
						_this.showview = sceneObj.showview;
						// uni.setStorageSync('StoresID', sceneObj.StoresID)
					}
				}
				/* 正常情况，包括H5端、APP端 */
				if (invite) {
					console.log(3);
					console.log(invite);
					_this.sId = invite;
					console.log(_this.sId);
					// uni.setStorageSync('StoresID', invite.StoresID)
				}
			}
			
			if(this.sId){
				uni.setStorageSync('StoresID', this.sId)
			}
			uni.hideLoading();
			_this.login();

			
			// if (uni.getStorageSync('openid')) {
			// 	this.StudentLoginF();
			// } else {
			// 	this.login();
			// }
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
				uni.navigateTo({
					url: '../yuyue/yuyue'
				});
			},
			toka() {
				uni.navigateTo({
					url: '../mycard/mycard'
				});
			},
			toxiaoxi() {
				uni.navigateTo({
					url: '../notice/notice'
				});
			},

			login() {
				uni.showLoading({
					title: "加载中..."
				})
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
								uni.setStorageSync('StoresID', resData.StoresID)
								console.log(resData)
								//openId、或SessionKdy存储//隐藏loading
								uni.setStorageSync('openid', resData.openid)
								if(resData.templateType == '1'){
									uni.setStorageSync('theme', 'blue');
									uni.setBackgroundColor({
									    backgroundColor: '#ffffff',
									});
								}else if(resData.templateType == '2'){
									uni.setStorageSync('theme', 'pink');
									uni.setBackgroundColor({
									    backgroundColor: '#ffffff',
									});
								}else if(resData.templateType == '3'){
									uni.setStorageSync('theme', 'black');
									uni.setBackgroundColor({
									    backgroundColor: '#191919',
									});
								}else{
									uni.setStorageSync('theme', 'green');
									uni.setBackgroundColor({
									    backgroundColor: '#ffffff',
									});
									
								}
								if(_this.showview == '2'){
									console.log(222)
									uni.reLaunch({
										url: '../saoma/saoma'
									});
								}else{
									uni.reLaunch({
										url: '../homepage/homepage'
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
							uni.setStorageSync('Phone', resData.htStores.Phone);
							uni.setStorageSync('KFQrCode', resData.htStores.KFQrCode);
							uni.setStorageSync('Address', resData.htStores.Address);
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
