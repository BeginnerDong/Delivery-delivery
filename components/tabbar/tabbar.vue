<template>
	
	<!--底部tab键-->
	<view class="navbar">
		<!-- <view class="navbar_item"  @click="webSelf.$Router.redirectTo({route:{path:'/pages/index/index'}})">
			<view class="nav_img">
				<image src="../../static/images/nabar1-a.png"/>
			</view>
			<view class="text this-text">首页</view>
		</view>
		<view class="navbar_item">
			<view class="nav_img">
				<image src="../../static/images/nabar2.png"/>
			</view>
			<view class="text">案件提交</view>
		</view>
		<view class="navbar_item" >
			<view class="nav_img">
				<image src="../../static/images/nabar3.png"/>
			</view>
			<view class="text">我的</view>
		</view> -->	
	</view>
	

</template>

<script>
	export default {
		props: {
			list: Array
		},
		
		data() {
			return {
				currIndex: 0,
				basic_blue:this.$AssetsConfig.basic_blue,
				Router:this.$Router
			};
		},
		
		mounted:function(){
			const self = this;
			console.log('$AssetsConfig',this.$AssetsConfig)
			var route = getCurrentPages().route;
		},
		
		methods: {
			// intoPath(){
			// 	const self = this;
			// 	const sale_token = uni.getStorageSync('sale_token');
			// 	if(sale_token){
			// 		self.$Router.redirectTo({route:{path:'/pages/salesman/salesman'}});
			// 	}else{
			// 		self.$Router.redirectTo({route:{path:'/pages/login/login'}});
			// 	};	
			// }
		}
	}
</script>

<style >

	
</style>
