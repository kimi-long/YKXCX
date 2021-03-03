<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="less">
	 @import './common/uni.css';
	 @import './common/iconfont.css';
	 @import "./static/css/bluestyle.css";
	 @import url('@/static/css/bluestyleless.less');
	 @import url('@/static/css/pinkstyleless.less');
	 @import url('@/static/css/greenstyleless.less');
	 @import url('@/static/css/blackstyleless.less');
	
	.uni-dialog-input{
		text-align: center;
	}

</style>

