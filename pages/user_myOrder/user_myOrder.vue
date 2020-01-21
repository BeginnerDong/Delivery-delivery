<template>
	<div>
		<div class="orderNav">
			<div class="tt" :class="num==1?'on':''" @click="change('1')">全部</div>
			<div class="tt" :class="num==2?'on':''" @click="change('2')">待接单</div>
			<div class="tt" :class="num==3?'on':''" @click="change('3')">进行中</div>
			<div class="tt" :class="num==4?'on':''" @click="change('4')">已完成</div>
		</div>
		
		<div class="orderList pdlr4">
			<ul>
				<li v-for="(item,index) in mainData" :key="index">
					<a class="infor" :data-id="item.id" @click="Router.navigateTo({route:{path:'/pages/user_myOrderDetail/user_myOrderDetail?id='+$event.currentTarget.dataset.id}})">
						<div class="datt flexRowBetween bordB1">
							<div class="left">
								<div class="color6" style="margin-bottom: 10rpx;">{{item.create_time}}</div>
								<div class="color9">订单编号：{{item.order_no}}</div>
							</div>
							<div class="state" v-if="item.transport_status==1">待接单</div>
							<!-- <div class="state" v-if="item.transport_status==2">骑手已接单</div> -->
							<div class="state" v-if="item.transport_status==2">进行中</div>
							<div class="state" v-if="item.transport_status==3">已完成</div>
						</div>
						<div class="msg pdlr10 pr">
							<div class="lable" v-if="item.type==1">取送件</div>
							<div class="lable" v-if="item.type==2">代办</div>	
							<div class="lable" v-if="item.type==3">代买</div>	
							<div class="lable" v-if="item.type==4">当日达</div>	
							<span><i class="dian"></i>{{item.start_site}}</span>
							<span><i class="dian red" v-if="item.end_site!=''"></i>{{item.end_site}}</span>
							<span><img class="icon" src="../../static/images/order-icon.png" >{{item.title}}</span>
							<span><img class="icon" src="../../static/images/order-icon1.png" >{{item.start_time}}</span>
						</div>
					</a>
					<div class="Rmny bordB1"  @click="moneyMxShow(index)">
						<span class="price">{{item.price}}</span>
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
					<div class=" pd10 flexRowBetween" v-if="item.transport_status==2||item.transport_status==3">
						<div class="flex" @click="callPhone(index)">
							<img class="persIcon" src="../../static/images/details-icon.png" >
							<span >{{item.rider.name}}&nbsp;&nbsp;&nbsp;&nbsp;{{item.rider.phone}}</span>
						</div>
						
						<img class="phoneIcon" src="../../static/images/details-icon1.png" alt="">
					</div>
					
					<div class="pd10 underBtn"  v-if="item.transport_status==1||item.transport_status==2">
						<!-- <span class="Bbtn">取消订单</span> -->
						<span class="Bbtn" @click="xiaofeiShow(index)">追加小费</span>
					</div>
				</li>
				
			</ul>
		</div>
		
		<!-- 费用明细弹框 -->
		<div class="black-bj" v-show="is_show"></div>
		<div class="fxmxShow" v-show="is_moneyMxShow">
			<div class="q-close fs24 mgr10" @click="moneyMxClose"><em class="color3">x</em></div>
			<div class="center line40">费用明细</div>
			<div class="infor fs12 color6">
				<p class="flexRowBetween" v-for="(item,index) in moneyMxDate">
					<span>{{item.title}}<i class="color9 mgl10">{{item.range}}</i></span>
					<em class="red">{{item.price}}</em>
				</p>
			</div>
		</div>
		
		<!-- 追加消费弹框 -->
		<div class="xiaofeiShow" v-show="is_xiaofeiShow">
			<div class="q-close fs24 mgr10" @click="xiaofeiClose"><em class="color3">x</em></div>
			<div class="line40">请输入价格</div>
			<div class="edit flex pdt15 pdb5 bordB1">
				<i class="mgr5">￥</i>
				<input type="text" v-model="submitData.price"  placeholder="请输入">
			</div>
			<div class="submitbtn mgt40">
				 <button class="btn" type="button" style="width: 100%; margin-bottom: 10px;" @click="addOrder">确定</button>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				Router:this.$Router,
				Utils:this.$Utils,
				is_show:false,
				mainData: [],
				num:1,
				is_moneyMxShow:false,
				moneyMxDate:[
					
				],
				is_xiaofeiShow:false,
				searchItem:{
					thirdapp_id: 2,
					pay_status:1,
					type:['in',[1,2,3,4]]
				},
				submitData:{
					price:''
				},
				xfIndex:'',
				orderId:''
			}
		},

		onLoad(options) {
			const self = this;
			if(options.num){
				self.num=options.num;
			};
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
			
			callPhone(index){
				const self = this;
				uni.makePhoneCall({
					phoneNumber:self.mainData[index].rider.phone
				})
			},
			
			pay() {
				const self = this;
				const postData = {};	
				postData.wxPay = {
					price:parseFloat(self.submitData.price).toFixed(2)
				};
				postData.tokenFuncName = 'getProjectToken',
				postData.searchItem = {
					id: self.orderId
				};
				postData.payAfter = [
					{
						tableName: 'Order',
						FuncName: 'update',
						data: {
							price:(parseFloat(self.mainData[self.xfIndex].price) + parseFloat(self.submitData.price)).toFixed(2),
							gratuity:(parseFloat(self.mainData[self.xfIndex].gratuity) + parseFloat(self.submitData.price)).toFixed(2)
						},
						searchItem:{
							id:self.mainData[self.xfIndex].id
						}
					},
				];
				
				const callback = (res) => {
					if (res.solely_code == 100000) {
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
										self.getMainData(true)
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
								
								self.getMainData(true)
								
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
			
			addOrder() {
				const self = this;
				uni.setStorageSync('canClick', false);	
				if(self.orderId!=''){
					self.pay();
					return
				};
				const postData = {};	
				/* postData.wxPay = {
					price:parseFloat(self.submitData.price).toFixed(2)
				}; */
				postData.tokenFuncName = 'getProjectToken',
				postData.data = {
					//parent_no:self.mainData.parentOrder[0].order_no,
					price:parseFloat(self.submitData.price).toFixed(2)
				}
				const callback = (res) => {
					if (res.solely_code == 100000) {
						self.orderId = res.info.id;
						self.pay()
					}else{
						self.$Utils.showToast(res.msg, 'none')
					}
				};
				self.$apis.addVirtualOrder(postData, callback);
			},
			
			change(num) {
				const self = this;
				if (num != self.num) {
					self.num = num;
					if(self.num==1){
						delete self.searchItem.transport_status
					}else if(self.num==2){
						self.searchItem.transport_status =['in',[1]]
					}else if(self.num==3){
						self.searchItem.transport_status =['in',[2]]
					}else if(self.num==4){
						self.searchItem.transport_status =['in',[3]]
					}
					self.getMainData(true)
				}
			},
			
			getMainData(isNew) {
				const self = this;
				var now = Date.parse(new Date())/1000;
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
				postData.searchItem.invalid_time = ['>',now];
				postData.getAfter = {
					rider: {
						tableName: 'UserInfo',
						middleKey: 'rider_no',
						key: 'user_no',
						searchItem: {
							status: 1,
							user_type:1
						},
						condition: '=',
						info:['name','phone']
					}
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData.push.apply(self.mainData, res.info.data);
					}
					console.log('self.mainData', self.mainData)
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.orderGet(postData, callback);
			},
			
			moneyMxClose(){
				const self = this;
				self.moneyMxDate = [];
				self.is_show = !self.is_show
				self.is_moneyMxShow = !self.is_moneyMxShow
			},
			
			moneyMxShow(index){
				const self = this;
				self.moneyMxDate.push({title:'基础配送费',price:'￥'+self.mainData[index].main_price});
				if(parseFloat(self.mainData[index].distance_price)>0){
					self.moneyMxDate.push({title:'距离附加费',price:'￥'+self.mainData[index].distance_price})
				};
				if(parseFloat(self.mainData[index].weight_price)>0){
					self.moneyMxDate.push({title:'重量附加费',price:'￥'+self.mainData[index].weight_price})
				};
				if(parseFloat(self.mainData[index].night_price)>0){
					self.moneyMxDate.push({title:'夜间配送费',price:'￥'+self.mainData[index].night_price})
				};
				if(parseFloat(self.mainData[index].weather_price)>0){
					self.moneyMxDate.push({title:'恶劣天气附加费',price:'￥'+self.mainData[index].weather_price})
				};
				if(parseFloat(self.mainData[index].holiday_price)>0){
					self.moneyMxDate.push({title:'节假日附加费',price:'￥'+self.mainData[index].holiday_price})
				};
				if(parseFloat(self.mainData[index].rush_price)>0){
					self.moneyMxDate.push({title:'高峰时段附加费',price:'￥'+self.mainData[index].rush_price})
				};
				if(parseFloat(self.mainData[index].insured_price)>0){
					self.moneyMxDate.push({title:'保价费',price:'￥'+self.mainData[index].insured_price})
				};
				if(parseFloat(self.mainData[index].gratuity)>0){
					self.moneyMxDate.push({title:'小费',price:'￥'+self.mainData[index].gratuity})
				};
				self.moneyMxDate.push({title:'总计',price:'￥'+self.mainData[index].price})
				self.is_show = !self.is_show
				self.is_moneyMxShow = !self.is_moneyMxShow
			},
			
			xiaofeiClose(){
				const self = this;
				self.is_show = false;
				self.is_xiaofeiShow = false
			},
			
			xiaofeiShow(index){
				const self = this;
				self.xfIndex = index;
				self.is_show = !self.is_show
				self.is_xiaofeiShow = !self.is_xiaofeiShow
			},
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/order.css";
	@import "../../assets/style/orderNav.css";
	page{
		background: #f5f5f5;
	}
	/* 我的收益 */
	.myExtendTop{width: 100%; text-align: center;box-sizing: border-box;padding: 20px 4%; background: #2FA0ED;}
	.myExtendTop .bigNum{font-size: 40px; padding: 0 4%; line-height:44px;}
	.myExtendTop .yuan{ font-size:14px; padding:5px 0 15px 0;}
	.myExtendTop .txBtn{ width: 100px; height: 30px;line-height: 30px; background: #fff; color: #333; border-radius: 3px; margin: 0 auto;font-size: 13px; display: block;}
	
	/* 信息RowBetween */
	.myRowBetween .item{ padding: 10px 4%;border-bottom: 1px solid #eee;background: #fff;}
	.myRowBetween .item .left{width: 50%;}
	.myRowBetween .item .time{color: #999; font-size: 12px;}
	.myRowBetween .item .left .time{margin-top: 5px;}
	.myRowBetween .item .right{width: 50%;text-align: right;font-size: 15px; display: flex; justify-content: flex-end;align-items: center;}
</style>	



