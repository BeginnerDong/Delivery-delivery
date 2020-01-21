<template>
	<view class="swiper-box" :style="'width:'+screenWidth+'px;height:'+screenWidth+'px'">
		<swiper class="swiper" :indicator-dots="false" :autoplay="true" :interval="3000" :duration="500" :circular="true"
		 @change="change" :style="'width:'+screenWidth+'px;height:'+screenWidth+'px'">
			<swiper-item v-for="(item,index) in list" :key="index" @click="item.src?webSelf.$Router.redirectTo({route:{path:item.src}}):''">
				<view class="swiper-item" :style="'width:'+screenWidth+'px;height:'+screenWidth+'px'">
					<image class="swiper-item" :src="item.url?item.url:''" mode=""></image>
				</view>
			</swiper-item>
		</swiper>
		<!-- dots -->
		<view class="dtos">
			<view class="dto" :class="{'dto-active':index===currIndex}" v-for="(item,index) in list" :key="index"></view>
		</view>
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
				webSelf:this,
				screenWidth:0
			};
		},
		mounted:function(){
			console.log('onLoad');
			this.screenWidth = uni.getSystemInfoSync().screenWidth;
			console.log('this.screenWidth',this.screenWidth)
		},
		
		
		
		methods: {
			change(s) {
				this.currIndex = s.detail.current;
			}
		}
	}
</script>

<style >
	.swiper-box {
		width: 100%;
		height: 750upx;
		background: #fff;
	}

	.swiper {
		width: 100%;
		height: 750upx;
		
	}

	.swiper-item {
		width: 100%;
		height: 750upx;
	}

	.dtos {
		display: flex;
		justify-content: center;
		margin-top: -20upx;
		width: 100%;
		position: absolute;
	}
	
	.dto {
		width: 14upx;
		height: 14upx;
		border-radius: 500upx;
		background: #e5e5e5;
		margin: 0 7upx;
		transition: width 0.5s;
	}
	
	.dto-active {
		background: #c2c2c2;
		width: 26upx;
	}
</style>
