<template>
        <view class="Navigation__NavigationWrapper-sc-163do31-0 heqysv react-draggable">
            <view class="Navigation__PageRedirectWrapper-sc-163do31-1 begceX">
                <view class="back" @tap="goback"><text
                    class="iconfont iconyou themeColor"></text></view>
            </view>
            <view class="Navigation__PageRedirectWrapper-sc-163do31-1 begceX">
                <view class="home" @click="toIndex"><text class="iconfont iconzhuye themeColor"></text></view>
            </view>
        </view>
</template>

<script>
	export default {
		data() {
			return {
				
			};

		},
		methods:{
			goback(){
				uni.navigateBack();
			},
			toIndex(){
				uni.reLaunch({
					url: '../index/index'
				});
			},
		}
	}
</script>

<style lang="less">
.iconfont{
	font-size: 26px;
}
</style>
