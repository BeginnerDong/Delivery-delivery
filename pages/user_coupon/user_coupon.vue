<template>
	<div style="padding-bottom: 50px;">
		<div class="couponList">
			<ul>
				<li v-for="(item,index) in mainData" :key="index" style="position: relative;">
					<img src="../../static/images/coupon_icon1.png" style="position: absolute;top:0;left:0;width: 100%;height: 100%;"></img>
					<div class="left" style="z-index: 999;">
						<div class="mny">{{item.value}}</div>
						<div class="">
							<div class="tit">优惠券</div>
							<p class="time">{{item.createTime}}~{{item.invalidTime}}</p>
						</div>
					</div>
					<div style="z-index: 999;" class="right">直接抵扣</div>
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				mainData:[],
				searchItem:{
					use_step:1
				},
			}
		},

		onLoad(options) {
			const self = this;
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			self.$Utils.loadAll(['getMainData'], self);
		
		},
		
		onReachBottom() {
			console.log('onReachBottom')
			const self = this;
			if (!self.isLoadAll && uni.getStorageSync('loadAllArray')) {
				self.paginate.currentPage++;
				self.getMainData()
			};
		},

		methods: {
			
			getMainData(isNew) {
				const self = this;
				if (isNew) {
					self.mainData = [];
					self.paginate = {
						count: 0,
						currentPage: 1,
						is_page: true,
						pagesize: 10
					}
				};
				const postData = {};
				postData.tokenFuncName = 'getProjectToken';
				postData.paginate = self.$Utils.cloneForm(self.paginate);
				postData.searchItem = self.$Utils.cloneForm(self.searchItem);
				
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData.push.apply(self.mainData, res.info.data);
						for (var i = 0; i < self.mainData.length; i++) {
							self.mainData[i].createTime = self.mainData[i].create_time.substr(0,10);
							self.mainData[i].invalidTime = self.$Utils.timeto(self.mainData[i].invalid_time,'ymd')
						}
					}
					console.log('self.mainData', self.mainData)
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.userCouponGet(postData, callback);
			},
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	.couponList{padding: 0 5%;}
	.couponList li{ width: 300px; height: 108px;box-sizing: border-box; margin: 20px auto 0 auto; display: flex; justify-content: space-between;align-items: center;padding: 15px;box-sizing: border-box;}
	.couponList li .left{width: 80%; display: flex; justify-content: center;align-items: center; color: #fef57c;}
	.couponList li .left .mny{font-size: 30px;margin-right: 5px;line-height: 50px; height: 50px;  }
	.couponList li .left .mny::before{content: '￥';font-size: 14px; font-weight: normal; }
	.couponList li .left .tit{font-size: 20px;letter-spacing:4px;line-height: 30px;}
	.couponList li .left .time{font-size: 10px; color: #fff;}
	.couponList li .right{width: 11%; display: flex; justify-content: center; align-items: center;text-align: right; color: #fff;font-size: 12px;padding: 5px;}
</style>	



