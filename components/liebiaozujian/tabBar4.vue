<template>
	<view class="tabBar">
		<view
		 v-for="(item,index) in tabBar" 
		 :key="item.url" 
		 class="tabbar_item" 
		 :class="{'active':item.url == currentPage}"
		 @click="navTo(item)"
		 >
			<image v-if="item.url == currentPage" :src="item.imgClick" mode=""></image>
			<image v-else :src="item.imgNormal" mode=""></image>
			<view class="text">{{item.text}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			currentPage:{
				type:String,
				default:'index'
			}
		},
		data() {
			return {
				tabBar:[{
						url:'index',
						text:'短模板',
						imgNormal:'../static/index_change.png',
						imgClick:'../static/index.png'
					},
					{
						url:'longIndex',
						text:'长模板',
						imgNormal:'../static/index_change.png',
						imgClick:'../static/index.png'
					},{
						url:'other',
						text:'其他示例',
						imgNormal:'../static/index_change.png',
						imgClick:'../static/index.png'
					}]
			};
		},
		created() {
			uni.hideTabBar({})
		},
		computed:{
			
		},
		methods:{
			navTo(item){
				if(item.url !== this.currentPage){
					var isUrl = `/pages/${item.url}/${item.url}`
					const that = this
					uni.switchTab({
						url: isUrl
					})
				} else{
					this.$parent.toTop()
				}
			}
		}
	}
</script>

<style lang="scss" scoped>

</style>
