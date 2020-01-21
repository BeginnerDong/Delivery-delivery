<template>
	<div>
		<!-- card -->
		<div class="VIPser_car">
			<div class="card pr">
				<img class="pic" :src="mainData.mainImg&&mainData.mainImg[0]?mainData.mainImg[0].url:''">
				<div class="mny">￥{{mainData.price}}</div>
			</div>
			
		</div>
		
		<div class="msg pdlr4 color6">
			<view class="content ql-editor" v-html="mainData.content">
			</view>
		</div>
		
		
		<div class="submitbtn" style="margin-top: 60px;">
			<button class="btn" type="submit" @click="submit">立即购买</button>
		</div>
	</div>
</template>

<script>
	export default {
		
		data() {
			return {
				Router: this.$Router,
				mainData: {}
			}
		},
		
		onLoad(options) {
			const self = this;
			self.id = options.id;
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			self.$Utils.loadAll(['getMainData'], self);
		},

		methods: {
			
			submit(){
				const self = this;
				uni.setStorageSync('canClick',false);
				
				var orderList = [
					{product_id:self.mainData.id,count:1,type:7}
				];
				self.addOrder(orderList)	
			},
			
			addOrder(orderList) {
				const self = this;	
				if(self.orderId){
					 self.pay();
					 return
				};
				const postData = {}; 
				postData.orderList = self.$Utils.cloneForm(orderList);
				postData.tokenFuncName = 'getProjectToken';
				const callback = (res) => {
					if (res && res.solely_code == 100000) {
						self.orderId = res.info.id;
						self.pay()
					} else {		
						uni.setStorageSync('canClick', true);
						uni.showToast({
							title: res.msg,
							duration: 2000
						});
					};		
				};
				self.$apis.addOrder(postData, callback);
			},
			
			
			
			pay(order_id) {
				const self = this;	
				var nowTime = Date.parse(new Date());
				const postData = {};	
				postData.wxPay = {
					price: self.mainData.price
				};
				postData.tokenFuncName = 'getProjectToken',
				postData.searchItem = {
					id: self.orderId
				};	
				if(parseInt(self.userInfoData.member_time)>now){
					self.time = parseInt(self.userInfoData.member_time) +self.mainData.duration
				}else{
					self.time = now + self.mainData.duration;
				};
				postData.payAfter = [{
					tableName: 'UserInfo',
					FuncName: 'update',
					data: {
						member_time: self.time
					},
					searchItem:{
						user_no:uni.getStorageSync('user_info').user_no
					}
				}, ];
				const callback = (res) => {
					if (res.solely_code == 100000) {
						uni.setStorageSync('canClick', true);
						if (res.info) {
							const payCallback = (payData) => {
								console.log('payData', payData)
								if (payData == 1) {
									uni.showToast({
										title: '支付成功',
										duration: 1000,
										success: function() {
											
										}
									});
									setTimeout(function() {
										self.$Router.redirectTo({route:{path:'/pages/shopOrder/shopOrder'}})
									}, 1000);
								} else {
									uni.setStorageSync('canClick', true);
									uni.showToast({
										title: '支付失败',
										duration: 2000
									});
								};
							};
							self.$Utils.realPay(res.info, payCallback);
						} else {
							
							uni.showToast({
								title: '支付成功',
								duration: 1000,
								success: function() {
									
								}
							});
							setTimeout(function() {
								self.$Router.redirectTo({route:{path:'/pages/shopOrder/shopOrder'}})
							}, 1000);
						};
					} else {
						uni.setStorageSync('canClick', true);
						uni.showToast({
							title: res.msg,
							duration: 2000
						});
					};
				};
				self.$apis.pay(postData, callback);
			},
			
			getMainData() {
				const self = this;
				console.log('852369')
				const postData = {};
				postData.searchItem = {
					thirdapp_id:2,
					type:2
				};
				const callback = (res) => {
					if (res.solely_code == 100000 && res.info.data[0]) {
						self.mainData = res.info.data[0];
			
						const regex = new RegExp('<img', 'gi');
						self.mainData.content = self.mainData.content.replace(regex, `<img style="max-width: 100%;"`);
					} else {
						self.$Utils.showToast(res.msg, 'none')
					};
					self.getUserInfoData()
					
				};
				self.$apis.productGet(postData, callback);
			},
			
			getUserInfoData() {
				const self = this;
				console.log('852369')
				const postData = {};
				postData.searchItem = {
					thirdapp_id:2,
				};
				postData.tokenFuncName = 'getProjectToken';
				const callback = (res) => {
					if (res.solely_code == 100000 && res.info.data[0]) {
						self.userInfoData = res.info.data[0];
					} else {
						self.$Utils.showToast(res.msg, 'none')
					};
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.userInfoGet(postData, callback);
			},
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	.VIPser_car .card{ width: 251px;height: 162px;display: block;margin:30px auto;}
	.VIPser_car .card .pic{width: 100%; height: 100%; display: block;}
	.VIPser_car .card .mny{ font-size: 20px; color: #edce43; position: absolute; bottom: 10px; right: 20px;}
	.msg div{ padding-bottom: 10px; font-size: 13px; color: #333; line-height: 40rpx;}
	.msg .tit{ font-size: 15px; font-weight: bold;}
</style>
	
	
