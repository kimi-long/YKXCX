<template>
		<view :class="themeName">
			<view class="setting">
				<view class="settingCell">
					<view class="cellItemBig" @tap='changeHead'>
						<text class="cellItemHead" :style="{backgroundImage: 'url('+numberInfo.HeadUrl+')'}" v-if='numberInfo.HeadUrl'></text>
						<text class="cellItemHead" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></text>
						<text class="iconfont iconzuo"></text>
						<text class="youText">修改头像</text>
					</view>
					<view class="cellItem">
						<text class="cellItemText">手机</text>
						<text class="youText">{{phone}}</text>
					</view>
					<view class="cellItem" @tap='changeName'>
						<text class="cellItemText">姓名</text>
						<text class="iconfont iconzuo"></text>
						<text class="youText">{{Sname}}</text>
					</view>
					<view class="cellItem" @tap='changeSex'>
						<text class="cellItemText">性别</text>
						<text class="iconfont iconzuo"></text>
						<text class="youText">{{sex}}</text>
					</view>
					<view class="cellItem">
						<text class="cellItemText">生日</text>
						<text class="iconfont iconzuo"></text>
						<view class="datePickBox">
							<gpp-date-picker @onCancel="onCancel" @onConfirm="onConfirm" :startDate="startDate" :endDate="endDate" v-if='getdata'
							 :defaultValue="defaultValue">
								{{defaultValue}}
							</gpp-date-picker>
						</view>
					</view>
				</view>
				<view class="qiehuan themeColor" @tap='showqiehuan' style="margin: 20px 0 5px;" v-if='ZHList.length >1 '>切换账号</view>
				<view class="qiehuan themeColor" @tap='showqiehuan2'  v-if='STList.length >1 '>切换门店</view>
				<uni-popup ref="popup1" type="dialog" :maskClick='false'>
					<uni-popup-dialog :value='SnameCopy' mode="input" title="请输入姓名" :duration="2000" @confirm="confirmName"></uni-popup-dialog>
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
									<view style="font-size: 16px;">{{item.Name}}</view>
								</label>
							</radio-group>
						</view>
						<view class="popButtonBox">
							<view class="popButton" style="border-right: 1px solid #EFEFEF;" @tap='quxiao'>取消</view>
							<view class="popButton themeColor" @tap='queding'>确定</view>
						</view>
					</view>
				</uni-popup>
				<uni-popup ref="popup3" type="center" :maskClick='false'>
					<view class="popBox">
						<view class="popTitle">切换门店</view>
						<view class="popList">
							<radio-group @change="radioChange2">
								<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in STList" :key="item.StoresID">
									<view>
										<radio :value="item.StoresID" :checked="index === current2" />
									</view>
<!-- 									<view class="popListItemImg" :style="{backgroundImage: 'url('+numberInfo.HeadUrl+')'}" v-if='numberInfo.HeadUrl'></view>
									<view class="popListItemImg" style="background-image: url(../../static/images/bluestyle/moren.png);" v-else></view> -->
									<view style="font-size: 16px;" class="name">{{item.StoresName}}</view>
								</label>
							</radio-group>
						</view>
						<view class="popButtonBox">
							<view class="popButton" style="border-right: 1px solid #EFEFEF;" @tap='quxiao2'>取消</view>
							<view class="popButton themeColor" @tap='queding2'>确定</view>
						</view>
					</view>
				</uni-popup>
				<wyb-action-sheet ref="actionSheet" :options="options" @itemclick="onItemClick" />
			
				<flow-button v-if='showAn'></flow-button>
			</view>
		</view>

</template>

<script>
	import gppDatePicker from "@/components/gpp-datePicker/gpp-datePicker.vue"
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import wybActionSheet from '@/components/wyb-action-sheet/wyb-action-sheet.vue'
	import flowButton from '../../components/flowButton/flowButton.vue'
	import {
		pathToBase64,
		base64ToPath
	} from 'image-tools'
	import {
		editStuBirthday,
		editStuSex,
		editStuName,
		editStuHeadUrl,
		getStudentInfo,
		getListMembers,
		getListXcxStores
	} from '../../static/js/env.js'
	export default {
		components: {
			uniPopup,
			uniPopupDialog,
			wybActionSheet,
			flowButton,
			gppDatePicker
		},
		data() {
			return {
				current: 0,
				current2: 0,
				getdata: false,
				showAn: true,
				startDate: "1900-01-01",
				endDate: "2100-12-31",
				defaultValue: "2020-01-02",
				numberInfo: {
					HeadUrl: '',
					isMain: '',
				},
				headURL: "",
				sex: "",
				phone: "",
				Sname: "",
				SnameCopy: "",
				birthDay: "",
				options: ['男', '女'],
				ZHList: [],
				STList: [],
				themeName:'theme'+uni.getStorageSync('theme'),
			};
		},
		created() {
			this.getStudentInfoF();
			let today = new Date();
			let year = today.getFullYear();
			let month = today.getMonth() + 1;
			let day = today.getDate();
			this.endDate = year + '-' + month + '-' + day;
		},
		methods: {
			queding(){
				let newStu = '';
				this.ZHList.forEach((item,index)=>{
					if(item.value){
						newStu = item.ID;
					}
				})
				this.getStudentInfoF2(this.ZHList[this.current].ID);
				this.$refs.popup2.close();
			},
			queding2(){
				uni.setStorageSync('StoresID',this.STList[this.current2].StoresID);
				uni.reLaunch({
					url: '../index/index'
				});
			},
			quxiao(){
				this.$refs.popup2.close()
			},
			quxiao2(){
				this.$refs.popup3.close()
			},
			radioChange2: function(evt) {
				for (let i = 0; i < this.STList.length; i++) {
					if (this.STList[i].StoresID === evt.target.value) {
						this.current2 = i;
						
					}else{
						
					}
				}
			},
			radioChange: function(evt) {
				console.log(evt)
				for (let i = 0; i < this.ZHList.length; i++) {
					if (this.ZHList[i].ID === evt.target.value) {
						this.current = i;
						
					}else{
						
					}
				}
			},
			onCancel() {

			},
			onConfirm(e) {
				this.birthDay = this.defaultValue;
				this.editStuBirthdayF(e.dateValue);
			},
			changeSex() {
				this.$refs.actionSheet.showActionSheet();
			},
			confirmName(done, value) {
				if (value == '') {
					uni.showToast({
						title: '请输入姓名',
						icon: 'none',
						duration: 2000
					})
				} else {
					this.editStuNameF(value);
					done();
				}
			},
			showqiehuan() {
				this.current = 0;
				this.$refs.popup2.open()
			},
			showqiehuan2(){
				this.STList.forEach((item,index)=>{
					if(item.StoresID == uni.getStorageSync('StoresID')){
						this.current2 = index;
						return
					}
				})
				this.$refs.popup3.open()
			},
			changeName() {
				this.$refs.popup1.open()
			},
			getListXcxStoresF() {
				uni.request({
					url: getListXcxStores,
					method: 'POST',
					data: {
						openid: uni.getStorageSync('openid'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.STList = resData.data;
							this.STList.forEach((item, index) => {
								if (index == 0) {
									item.value = true;
								} else {
									item.value = false;
								}
							})
							console.log(this.ZHList);
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
			getListMembersF() {
				console.log(this.numberInfo);
				uni.request({
					url: getListMembers,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						isMain: this.numberInfo.isMain,
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.ZHList = resData.data;
							this.ZHList.forEach((item, index) => {
								if (index == 0) {
									item.value = true;
								} else {
									item.value = false;
								}
							})
							console.log(this.ZHList);
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
			editStuBirthdayF(value) {
				uni.showLoading({
					title: "加载中..."
				});
				uni.request({
					url: editStuBirthday,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						Birthday: value
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.getStudentInfoF();
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
			editStuSexF(value) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: editStuSex,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						Sex: value
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.getStudentInfoF();
							uni.hideLoading();
							uni.showToast({
								title: '修改成功',
								icon: 'success',
								duration: 2000
							})
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
			editStuNameF(value) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: editStuName,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						Name: value
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.getStudentInfoF();
							uni.hideLoading();
							uni.showToast({
								title: '修改成功',
								icon: 'success',
								duration: 2000
							})
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
			editStuHeadUrlF(data) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: editStuHeadUrl,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						StoresID: uni.getStorageSync('StoresID'),
						token: uni.getStorageSync('token'),
						imgdate: data
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							this.getStudentInfoF();
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
			changeHead() {
				let that = this;
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album', 'camera'], //从相册选择
					success: function(res) {
						console.log(JSON.stringify(res.tempFilePaths));
						console.log(that.options);
						pathToBase64(res.tempFilePaths[0])
							.then(base64 => {
								that.editStuHeadUrlF(base64);
							})

					}
				});
			},
			onItemClick(e) {
				console.log(e);
				this.editStuSexF(e.index + 1)
			},
			getStudentInfoF2(data) {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getStudentInfo,
					method: 'POST',
					data: {
						StudentID: data,
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;
						if (resData.orsuccess == "1") {
							uni.showToast({
								title: '切换成功',
								icon: 'success',
								duration: 2000
							})
							uni.setStorageSync('StudentID', data);
							this.numberInfo = resData.data;
							this.phone = resData.data.Phone;
							
							if(resData.data.Birthday != ''){
								this.defaultValue = resData.data.Birthday;
							}else{
								this.defaultValue = "暂未设置"
							};
							this.getdata = true;
							this.Sname = resData.data.Name;
							if (resData.data.Sex == '1') {
								this.sex = '男'
							} else if (resData.data.Sex == '2') {
								this.sex = '女'
							}
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
			getStudentInfoF() {
				uni.showLoading({
					title: "加载中..."
				})
				uni.request({
					url: getStudentInfo,
					method: 'POST',
					data: {
						StudentID: uni.getStorageSync('StudentID'),
						token: uni.getStorageSync('token'),
					},
					success: res => {
						let resData = res.data;

						if (resData.orsuccess == "1") {
							this.numberInfo = resData.data;
							this.phone = resData.data.Phone;
							this.birthDay = resData.data.Birthday;
							if(resData.data.Birthday != ''){
								this.defaultValue = resData.data.Birthday;
							}else{
								this.defaultValue = "暂未设置"
							};
							this.getdata = true;
							this.Sname = resData.data.Name;
							if (resData.data.Sex == '1') {
								this.sex = '男'
							} else if (resData.data.Sex == '2') {
								this.sex = '女'
							}
							this.getListMembersF();
							this.getListXcxStoresF();
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
