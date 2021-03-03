<template>
	<view :class="themeName">
		<view  class="pagecomment">
			<view class="container999" @touchstart="refreshStart" @touchmove="refreshMove" @touchend="refreshEnd" >
				<!-- 刷新组件需搭配scroll-view使用，并在pages.json中添加 "disableScroll":true-->
				<refresh ref="refresh" @isRefresh='isRefresh'></refresh>
				<view class='nav'>
			
					<!-- 搜索 -->
					<!-- 			<view class='searchInput999'>
						<view class='searchBox999'>
							<image src='/static/icon-search.png' class='search999'></image>
						</view>
						<input class='input999' placeholder="输入关键词"></input>
					</view> -->
					<!-- 导航栏 agents导航栏标题 -->
					<navTab ref="navTab" :tabTitle="tabTitle" @changeTab='changeTab'></navTab>
				</view>
				<view class="secondTab">
					<text :class="activeSTab == 0?'themeColor':''" class="secondTabItem" @tap="changeSe(0)">全部({{allCount[currentTab]}})</text>
					<text :class="activeSTab == 1?'themeColor':''" class="secondTabItem" @tap="changeSe(1)">好评({{haoCount[currentTab]}})</text>
					<text :class="activeSTab == 2?'themeColor':''" class="secondTabItem" @tap="changeSe(2)">普通({{putongCount[currentTab]}})</text>
					<text :class="activeSTab == 3?'themeColor':''" class="secondTabItem" @tap="changeSe(3)">差评({{chapingCount[currentTab]}})</text>
				</view>
				<!-- swiper切换 swiper-item表示一页 scroll-view表示滚动视窗 -->
				<swiper style="min-height: calc(100VH - 108px);" :current="currentTab" @change="swiperTab">
					<swiper-item v-for="(listItem,listIndex) in list" :key="listIndex">
						<scroll-view style="height: 100%;" scroll-y="true" @scrolltolower="lower1" scroll-with-animation :scroll-into-view="toView">
							
							<view class='content' v-if="listItem.length > 0">
								<cell class="yuyueListItem" v-for="(item,index) in listItem" :key='index'>
									<view class="commentItem">
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
			
								</cell>
								<view class="mybnWMth">已到底部</view>
								<!-- <view style="width: 100%;height: 150upx;opacity:0;">底部占位盒子</view> -->
							</view>
			
							<view class="kong noCard" v-if='listItem.length == 0'>
								<view class="iconfont iconkong themeColor"></view>
								<view class="zanwu">暂无评价</view>
							</view>
						</scroll-view>
					</swiper-item>
				</swiper>
				<flow-button></flow-button>
			</view>
		</view>
	</view>


</template>

<script>
	const util = require('../../util/util.js');
	import refresh from '../../components/liebiaozujian/refresh.vue';
	import navTab from '../../components/liebiaozujian/navTab.vue';
	import tabBar4 from '../../components/liebiaozujian/tabBar4.vue';
	import flowButton from '../../components/flowButton/flowButton.vue'
	import {
		getListEvaluate
	} from '../../static/js/env.js'
	export default {
		components: {
			refresh,
			navTab,
			tabBar4,
			flowButton
		},
		data() {
			return {
				activeSTab:0,
				allCount:[0,0],
				haoCount:[0,0],
				putongCount:[0,0],
				chapingCount:[0,0],
				toView: '', //回到顶部id
				tabTitle: ['课程评价', '老师评价'], //导航栏格式 --导航栏组件
				currentTab: 0, //sweiper所在页
				pages: [1, 1], //第几个swiper的第几页
				list: [
					[],
					[]
				], //数据格式
				finishList: [false, false],
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		onLoad(e) {
			this.getListEvaluateF(1);
			this.getListEvaluateF(2);
		},
		onShow() {},
		onHide() {},
		methods: {
			changeSe(data){
				this.activeSTab = data;
				if(this.currentTab == 0){
					this.finishList[0] = false;
					this.list[0] = [];
					this.pages[0] = 1;
				}else{
					this.finishList[1] = false;
					this.list[1] = [];
					this.pages[1] = 1;
				}
				this.$forceUpdate()
				this.getListEvaluateF(this.currentTab+1);
			},
			getListEvaluateF(data) {
				
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getListEvaluate,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						StudentID: uni.getStorageSync('StudentID'),
						EvaluateType: this.activeSTab,
						IType: data,
						pages: this.pages[this.currentTab],
						psize: 10,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							if (data == 1) {
								this.allCount[0] = resData.allcount0;
								this.haoCount[0] = resData.allcount1;
								this.putongCount[0] = resData.allcount2;
								this.chapingCount[0] = resData.allcount3;
								if (resData.data.length != 10) {
									this.finishList[0] = true;
								} else {
									this.finishList[0] = false;
								}
							} else {
								this.allCount[1] = resData.allcount0;
								this.haoCount[1] = resData.allcount1;
								this.putongCount[1] = resData.allcount2;
								this.chapingCount[1] = resData.allcount3;
								if (resData.data.length != 10) {
									this.finishList[1] = true;
								} else {
									this.finishList[1] = false;
								}
							}
							resData.data.forEach((item, index) => {
								if (data == 1) {
									this.list[0].push(item);
								} else {
									this.list[1].push(item);
								}
							})
							this.$forceUpdate()
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
			toTop() {
				this.toView = ''
				setTimeout(() => {
					this.toView = 'top' + this.currentTab
				}, 10)
			},
			changeTab(index) {
				this.currentTab = index;
			},
			// 其他请求事件 当然刷新和其他请求可以写一起 多一层判断。
			isRequest() {
				return new Promise((resolve, reject) => {
					this.pages[this.currentTab]++
					let data = this.currentTab + 1;
					var that = this
					uni.request({
						url: StuGetListPreAbout,
						method: 'POST',
						data: {
							StoresID: uni.getStorageSync('StoresID'),
							token: uni.getStorageSync('token'),
							StudentID: uni.getStorageSync('StudentID'),
						    EvaluateType: this.activeSTab,
						    IType: data,
							pages: that.pages[that.currentTab],
							psize: 10,
						},
						success: res => {
							let resData = res.data;
							if (resData.orsuccess == "1") {
								// resData.data.forEach((item, index) => {
								// 	this.yuyueList.push(item);
								// })
								if (resData.data.length != 10) {
									if (this.currentTab == 0) {
										this.finishList[0] = true;
									} else if (this.currentTab == 1) {
										this.finishList[1] = true;
									} else if (this.currentTab == 2) {
										this.finishList[2] = true;
									} else {
										this.finishList[3] = true;
									}
								}
								uni.hideLoading()
								resolve(resData.data)
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


					// setTimeout(() => {
					// 	uni.hideLoading()
					// 	uni.showToast({
					// 		icon: 'none',
					// 		title: `请求第${that.currentTab + 1 }个导航栏的第${that.pages[that.currentTab]}页数据成功`
					// 	})
					// 	let newData = ['新数据1', '新数据2', '新数据3']
					// 	resolve(newData)
					// }, 1000)
				})
			},
			// swiper 滑动
			swiperTab: function(e) {
				var index = e.detail.current //获取索引
				this.$refs.navTab.longClick(index);
				this.changeSe(0);
				
			},
			// 加载更多 util.throttle为防抖函数
			lower1: util.throttle(function(e) {
				if (this.finishList[this.currentTab]) {
					return
				}
				console.log(`加载${this.currentTab}`) //currentTab表示当前所在页数 根据当前所在页数发起请求并带上page页数
				uni.showLoading({
					title: '加载中',
					mask: true
				})

				this.isRequest().then((res) => {
					let tempList = this.list
					tempList[this.currentTab] = tempList[this.currentTab].concat(res)
					this.list = tempList
					this.$forceUpdate() //二维数组，开启强制渲染
				})
			}, 300),
			// 刷新touch监听
			refreshStart(e) {
				this.$refs.refresh.refreshStart(e);
			},
			refreshMove(e) {
				this.$refs.refresh.refreshMove(e);
			},
			refreshEnd(e) {
				this.$refs.refresh.refreshEnd(e);
			},
			isRefresh() {
				let data = 0;
				if(this.currentTab == 0){
					this.list[0] = [];
					data = 1;
				}else{
					this.list[1] = [];
					data = 3;
				}
				this.getListEvaluateF(data);
				setTimeout(() => {
					uni.showToast({
						icon: 'success',
						title: '刷新成功'
					})
					this.$refs.refresh.endAfter() //刷新结束调用
				}, 1000)
			}
		}
	};
</script>

<style lang="scss">

</style>

