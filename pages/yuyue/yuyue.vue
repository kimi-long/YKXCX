<template>
	<view :class="themeName">
		<view class="pageyuyue">
			<view class="container999" @touchstart="refreshStart" @touchmove="refreshMove" @touchend="refreshEnd">
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
				<!-- swiper切换 swiper-item表示一页 scroll-view表示滚动视窗 -->
				<swiper style="min-height: 100vh;" :current="currentTab" @change="swiperTab">
					<swiper-item v-for="(listItem,listIndex) in list" :key="listIndex">
						<scroll-view style="height: 100%;" scroll-y="true" @scrolltolower="lower1" scroll-with-animation :scroll-into-view="toView">
							<view :id="'top'+listIndex" style="width: 100%;height: 120upx;"></view>
							<view class='content'  v-if="listItem.length > 0">
								<view class="yuyueListItem" v-for="(item,index) in listItem" :key='index' @tap='toLesson' :data-statec="item.state" :data-RowClassID="item.ID" :data-PreAboutID="item.PreAboutID">
									<view class="riliKeListItemImg" :style="{backgroundImage: 'url(' + item.CourseHeadUrl + ')'}">
										<text class="riliKeListItemImgText" v-if="currentTab == 0 ||currentTab == 3 ">已预约</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 1">已完成</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 0">学员取消</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 1">预约</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 2">签到</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 3">未到</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 4">机构取消</text>
										<text class="riliKeListItemImgText" v-if="currentTab == 2 && item.state == 5">请假</text>
									</view>
			
									<view class="riliKeListItemInfo">
										<view class="firstLine">
											<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '0'">班课</text>
											<text class="riliKeListItemInfoClass" v-if="item.RowClassType == '1'">私教</text>
											<text class="riliKeListItemInfoName">{{item.CourseName}}</text>
										</view>
										<view class="riliKeListItemInfoTime">
											{{item.SKtime}}  {{item.startTime}}
										</view>
										<view class="riliKeListItemInfoClassTime">
											<text class="riliKeListItemInfoClassTime" v-if='item.ConsumptionOfClass'>{{item.ConsumptionOfClass}}课时</text>
											<text class="riliKeListItemInfoClassPrice"  v-if='Math.round(item.singlePrice) != 0'>￥{{item.singlePrice}}</text>
										</view>
										<view class="riliKeListItemInfoClassTeacher">
										{{item.Name}}
										<text class="can" v-if="currentTab == 1">评价</text>
										<text class="can" v-else>详情</text>
										</view>
									</view>
								</view>
								<view class="mybnWMth">已到底部</view>
								<view style="width: 100%;height: 150upx;opacity:0;">底部占位盒子</view>
							</view>
			
							<view class="kong noCard" v-if='listItem.length == 0'>
								<view class="iconfont iconkong themeColor"></view>
								<view class="zanwu">暂无对应的预约</view>
								<view class="zanwu">浏览一下其他预约吧</view>
							</view>
							
						</scroll-view>
					</swiper-item>
				</swiper>
				<bottom-Bar :index="indexNum"></bottom-Bar>
			</view>
		</view>
	</view>

</template>

<script>
	const util = require('../../util/util.js');
	import refresh from '../../components/liebiaozujian/refresh.vue';
	import navTab from '../../components/liebiaozujian/navTab.vue';
	import tabBar4 from '../../components/liebiaozujian/tabBar4.vue';
	import {
		StuGetListPreAbout
	} from '../../static/js/env.js'
	import bottomBar from "@/components/bottomBar/bottomBar.vue";
	export default {
		components: {
			refresh,
			navTab,
			tabBar4,bottomBar
		},
		data() {
			return {
				themeName:'theme'+uni.getStorageSync('theme'),
				indexNum: 3,
				toView: '', //回到顶部id
				tabTitle: ['已预约', '已完成', '其他', '未签到'], //导航栏格式 --导航栏组件
				currentTab: 0, //sweiper所在页
				pages: [1, 1, 1, 1], //第几个swiper的第几页
				list: [
					[],
					[],
					[],
					[]
				],//数据格式
				finishList:[false,false,false,false]
			};
		},
		onLoad(e) {
			if(uni.getStorageSync('StudentID') == '0'){
				uni.reLaunch({
					url: '../login/login'
				});
			}else{
				this.StuGetListPreAboutF(1);
				this.StuGetListPreAboutF(2);
				this.StuGetListPreAboutF(3);
				this.StuGetListPreAboutF(4);
			}

		},
		onShow() {},
		onHide() {},
		methods: {
			toLesson(e){
				console.log(e);
				var RowClassID = e.currentTarget.dataset.rowclassid;
				var PreAboutID = e.currentTarget.dataset.preaboutid;
				var state = e.currentTarget.dataset.statec;
				let type = this.currentTab +1;
				uni.navigateTo({
					url: '../kechengDetail/kechengDetail?RowClassID='+RowClassID+'&state='+state+'&PreAboutID='+PreAboutID+'&type='+type
				});
			},
			StuGetListPreAboutF(data) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: StuGetListPreAbout,
					method: 'POST',
					data: {
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						StudentID: uni.getStorageSync('StudentID'),
						Type: data,
						pages: this.pages[this.currentTab],
						psize: 10,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							if(data == 1){
								if(resData.data.length != 10){
									this.finishList[0] = true;
								}else{
									this.finishList[0] = false;
								}
								
							}else if(data ==2){
								if(resData.data.length !=10){
									this.finishList[1] = true;
								}else{
									this.finishList[1] = false;
								}
							}else if(data == 3){
								if(resData.data.length !=10){
									this.finishList[2] = true;
								}else{
									this.finishList[2] = false;
								}
							}else if(data == 4){
								if(resData.data.length !=10){
									this.finishList[3] = true;
								}else{
									this.finishList[3] = false;
								}
							}
							resData.data.forEach((item, index) => {
								if(data == 1){
									this.list[0].push(item);
								}else if(data ==2){
									this.list[1].push(item);
								}else if(data == 3){
									this.list[2].push(item);
								}else{
									this.list[3].push(item);
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
					let data = this.currentTab +1;
					var that = this
					uni.request({
						url: StuGetListPreAbout,
						method: 'POST',
						data: {
							StoresID: uni.getStorageSync('StoresID'),
							token: uni.getStorageSync('token'),
							StudentID: uni.getStorageSync('StudentID'),
							Type: data,
							pages: that.pages[that.currentTab],
							psize: 10,
						},
						success: res => {
							let resData = res.data;
							if (resData.orsuccess == "1") {

								if(resData.data.length != 10){
									if(this.currentTab == 0){
										this.finishList[0] = true;
									}else if(this.currentTab == 1){
										this.finishList[1] = true;
									}else if(this.currentTab == 2){
										this.finishList[2] = true;
									}else{
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
			},
			// 加载更多 util.throttle为防抖函数
			lower1: util.throttle(function(e) {
				if(this.finishList[this.currentTab]){
					return
				}
				console.log(`加载${this.currentTab}`) //currentTab表示当前所在页数 根据当前所在页数发起请求并带上page页数
				uni.showLoading({
					title: '加载中',
					mask: true
				})
				console.log(this.finishList);
				console.log(this.finishList[this.currentTab]);

				this.isRequest().then((res) => {
					let tempList = this.list
					tempList[this.currentTab] = tempList[this.currentTab].concat(res)
					console.log(tempList)
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
				let data = this.currentTab +1
				if(this.currentTab == 0){
					this.list[0] = [];
				}else if(this.currentTab == 1){
					this.list[1] = [];
				}else if(this.currentTab == 2){
					this.list[2] = [];
				}else{
					this.list[3] = [];
				}
				this.$forceUpdate();
				console.log(this.list);
				this.StuGetListPreAboutF(data);
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

