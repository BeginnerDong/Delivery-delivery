<template>
	<div style="padding: 40px 0">
		<div class="orderList pdlr4">
			<ul>
				<li>
					<a class="infor" href="#">
						<div class="datt flexRowBetween bordB1">
							<div class="left">
								<div class="color6" style="margin-bottom: 10rpx;">{{mainData.create_time}}</div>
								<div class="color9">订单编号：{{mainData.order_no}}</div>
							</div>
							<div class="state" v-if="mainData.transport_status==1">待接单</div>
							<div class="state" v-if="mainData.transport_status==2">骑手已接单</div>
							<!-- <div class="state" v-if="mainData.transport_status==2">进行中</div> -->
							<div class="state" v-if="mainData.transport_status==3">已完成</div>
						</div>
						<div class="msg pdlr10 pr">
							<div class="lable" v-if="mainData.type==1">取送件</div>
							<div class="lable" v-if="mainData.type==2">代办</div>	
							<div class="lable" v-if="mainData.type==3">代买</div>	
							<div class="lable" v-if="mainData.type==4">当日达</div>	
							<span><i class="dian"></i>{{mainData.start_site}}</span>
							<span><i class="dian red"></i>{{mainData.end_site}}</span>
							<span><img class="icon" src="../../static/images/order-icon.png" >{{mainData.title}}</span>
							<span><img class="icon" src="../../static/images/order-icon1.png" >{{mainData.start_time}}</span>
						</div>
					</a>
					<div class="Rmny bordB1"  @click="moneyMxShow(index)">
						<span class="price">{{mainData.price}}</span>
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<div class="f5H10"></div>
				<div class="remarks pd10 bgwhite radius8" v-if="mainData.passage1!=''">
					<div class="">备注信息</div>
					<textarea placeholder="备注" v-model="mainData.passage1"></textarea>
				</div>
				<li>
					<div class=" pd10 flexRowBetween" v-if="mainData.transport_status==2">
						<div class="flex">
							<img class="persIcon" src="../../static/images/details-icon.png" >
							<span >{{mainData.rider.name}}&nbsp;&nbsp;&nbsp;&nbsp;{{mainData.rider.phone}}</span>
						</div>
						
						<img class="phoneIcon" src="../../static/images/details-icon1.png" alt="">
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
	</div>
</template>

<script>
	export default {
		data() {
			return {
				is_show:false,
				mainData: [],
				current:1,
				is_moneyMxShow:false,
				moneyMxDate:[
					
				]
			}
		},

		onLoad(options) {
			const self = this;
			self.id = options.id;
			self.$Utils.loadAll(['getMainData'], self);
		},

		methods: {
			
			getMainData() {
				const self = this;
				const postData = {};
				postData.tokenFuncName = 'getProjectToken';
				postData.searchItem = {
					thirdapp_id:2,
					id:self.id
				};
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
						self.mainData = res.info.data[0]
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
				self.moneyMxDate.push({title:'基础配送费',price:'￥'+self.mainData.main_price});
				if(parseFloat(self.mainData.distance_price)>0){
					self.moneyMxDate.push({title:'距离附加费',price:'￥'+self.mainData.distance_price})
				};
				if(parseFloat(self.mainData.weight_price)>0){
					self.moneyMxDate.push({title:'重量附加费',price:'￥'+self.mainData.weight_price})
				};
				if(parseFloat(self.mainData.night_price)>0){
					self.moneyMxDate.push({title:'夜间配送费',price:'￥'+self.mainData.night_price})
				};
				if(parseFloat(self.mainData.weather_price)>0){
					self.moneyMxDate.push({title:'恶劣天气附加费',price:'￥'+self.mainData.weather_price})
				};
				if(parseFloat(self.mainData.holiday_price)>0){
					self.moneyMxDate.push({title:'节假日附加费',price:'￥'+self.mainData.holiday_price})
				};
				if(parseFloat(self.mainData.rush_price)>0){
					self.moneyMxDate.push({title:'高峰时段附加费',price:'￥'+self.mainData.rush_price})
				};
				if(parseFloat(self.mainData.insured_price)>0){
					self.moneyMxDate.push({title:'保价费',price:'￥'+self.mainData.insured_price})
				};
				if(parseFloat(self.mainData.gratuity)>0){
					self.moneyMxDate.push({title:'小费',price:'￥'+self.mainData.gratuity})
				};
				self.moneyMxDate.push({title:'总计',price:'￥'+self.mainData.price})
				self.is_show = !self.is_show
				self.is_moneyMxShow = !self.is_moneyMxShow
			},
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/order.css";
	page{
		background: #f5f5f5;
	}
	.remarks textarea{width: 100%; height: 80px; display: block;padding:0px 10px; box-sizing: border-box; border: none; color: #666; margin-top: 10px;}
</style>	



