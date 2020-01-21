
<script>
	import token from "./common/token.js"
	export default {
		onLaunch: function() {	
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	@import "/assets/style/style.css";
	@import "/assets/style/quill.css";
	/* rgb(83, 125, 235) */
	
	uni-tabbar .uni-tabbar__icon{width: 44rpx;height: 44rpx;margin-top: 8rpx;}
	uni-page-head .uni-page-head__title{font-weight: normal;}
	uni-tabbar .uni-tabbar__label{line-height: 30rpx; margin-top: 10rpx;}
	uni-swiper .uni-swiper-dot{ height: 6rpx; width: 20rpx; background: #999;}
	.swiper-box .uni-swiper-dot-active{width: 30rpx;border-radius: 6rpx;}
	
	.ql-editor img{
		max-width: 100%;
		margin: 0 auto;
	}
	
	/* 效果图详情页轮播 */
	/* .sketchXq uni-swiper .uni-swiper-dots-horizontal{bottom: auto;top: 760rpx;} */
</style>
