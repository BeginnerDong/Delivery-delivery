<template>
	<div style="padding-bottom: 100px;">
		<div class="agentMsg pdlr4" >
			<div class="proNav">
				<ul>
					<li :class="clickCur==index?'on':''" v-for="(item,index) in buyData"  :key="index" 
					@click="clickPro(index)">
						<img class="icon" :src="item.mainImg&&item.mainImg[0]?item.mainImg[0].url:''">
						<span>{{item.title}}</span>
					</li>
				</ul>
			</div>
		</div>
		<div class="f5H10"></div>
		<div class="agentMsg pdlr4 mgt10" >
			<div class="editTex">
				<textarea rows="" cols="4" v-model="submitData.passage1"  placeholder="备注" style="font-size: 14px;"></textarea>
			</div>
		</div>
		<div class="bordB1"></div>
		<div class="qsLineinfor">
			<ul>
				<li class="flexRowBetween"   @click="dianfuShow" style="border-bottom: none;">
					<div class="left" style="width: 80%;"><em class="red fs12">骑手垫付商品费，送货时当面结清</em></div>
					<div class="right" style="width: 20%;">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<div class="f5H10"></div>
				
				<li class="flexRowBetween liContbox2" >
					<div class="left">购买：</div>
					<div class="right bordB1" style="justify-content: initial; flex-direction: column;">
						<div class="smallnav">
							<span class="mgr15" :class="num==1?'on':''" @click="seltChage('1')">指定地址
							<span class="pucolor fs10" style="border:none">更精准</span></span>
							<span :class="num==2?'on':''" @click="seltChage('2')">骑手就近购买
							<span class="color9 fs10" style="border:none">3公里内</span></span>
						</div>
						<div class="flexRowBetween pdt15" :style="startAddress.name?'color:black':''" @click="chooseAddress" style="width: 100%;" v-if="num==1">
							{{startAddress.name?startAddress.name:'请选择'}}
							
							<img class="arrowR" src="../../static/images/icon.png" >
						</div>
						
					</div>
				</li>
				
				<li class="flexRowBetween liContbox2">
					<div class="left">收货：</div>
					<div class="right" style="padding-bottom:0;" @click="Router.navigateTo({route:{path:'/pages/address/address?name=buy'}})">
						<input type="text" style="color: #000000;" v-model="buyAddress.city&&buyAddress.detail?buyAddress.city+buyAddress.detail:buyAddress.city" name="" disabled="true" value="" placeholder="选择收货地址" />
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween liContbox2" style="paddng: 0 4%;" v-if="buyAddress.name&&buyAddress.phone">
					<div class="left" style="color: #999;font-size: 13px;">
						<input type="text" style="color: #000000;" v-model="buyAddress.name"  disabled="true" value="" />
					</div>
					<div class="right" style="font-size: 13px;">
						<input type="text" style="color: #000000;" v-model="buyAddress.phone"  disabled="true" value="" />
					</div>
				</li>
				<div class="f5H10"></div>
				
				<li class="flexRowBetween">
					<div class="left">优惠券：</div>
					<div class="right"  v-if="couponData.length==0">
						暂无优惠券使用<img class="arrowR" src="../../static/images/icon.png" >
					</div>
					<div class="right" @click="couponShow" v-if="couponData.length>0&&chooseCoupon.length==0">
						请选择<img class="arrowR" src="../../static/images/icon.png" >
					</div>
					<div class="right" @click="couponShow" v-if="couponData.length>0&&chooseCoupon.length>0">
						优惠券抵扣-{{pay.coupon[0].price}}<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">小费：</div>
					<div class="right" @click="tippingShow" :style="submitData.gratuity>0?'color:#000000':''">
						{{submitData.gratuity>0?submitData.gratuity+'元':'给小费抢单更快哟'}}<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">购买时间：</div>
					<!-- <div class="right" @click="accessShow">
						<span class="fs14 color3">立即购买</span><img class="arrowR" src="../../static/images/icon.png" >
					</div> -->
					<hTimePicker sTime="0" cTime="24" interval="15" @changeTime="changeTime">
					  <view slot="pCon" class="changeTime">
					    {{submitData.start_time}}
					  </view>
					</hTimePicker>
				</li>
			</ul>
		</div>
		
		<div class="qusUnderFix flexRowBetween">
			<div class="left flex fs12" @click="moneyMxShow">
				总价&nbsp;<em class="price ftw">{{pay.wxPay?pay.wxPay.price:'0.00'}}</em>
				<img class="arrowR" src="../../static/images/icon.png" >
			</div>
			<div class="right"  @click="Utils.stopMultiClick(submit)">提交订单</div>
		</div>
		
		<!-- 弹框背景 -->
		<div class="black-bj" v-if="is_show"></div>
		
		<!-- 垫付商品费弹框 -->
		<div class="qjAlertCont dianfuShow" v-if="is_dianfuShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="dianfuShow">取消</span>
				<span>预估商品费</span>
				<span class="pucolor fs12" @click="dianfuShow">确定</span>
			</div>
			<div class="pdtb15 center color6 fs12">供骑手代购时参考(可选填)</div>
			<div class=" flexCenter pucolor" style="text-align: center; border: 1px solid #2FA0ED; width: 75%;margin: 0 auto;border-radius: 5px;padding: 5px; font-size: 13px;">
				预估￥&nbsp;<input type="text" v-model="submitData.value"  placeholder="请输入" style="width: 30%;">
			</div>
			<div class="pdt15 pdb30 center color6 fs12 flexCenter"><img src="../../static/images/icon1.png" style="width: 14px; height: 14px; display: block;">&nbsp;最高可代购价值500元的商品</div>
		</div>
		
		<!-- 购买时间弹框 -->
		<div class="qjAlertCont accessShow" style="padding-bottom: 80px;" v-if="is_accessShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="accessShow">取消</span>
				<span>购买时间</span>
				<span class="pucolor fs12">确定</span>
			</div>
			<div class="setData fs12 color6">
				<ul>
					<li class="flexRowBetween" v-for="(item,index) in setData" :key="index" @click="qjSetData(index)">
						<span>{{item.title}}</span>
						<span class="center">{{item.thatDay}}</span>
						<span class="seltBtn">
							<img class="icon" :src="seltData==index?'../../static/images/coupons-icon.png':'../../static/images/coupons-icon1.png'" alt="">
						</span>
					</li>
				</ul>
			</div>
		</div>
		
		<!-- 优惠券 -->
		<div class="qjAlertCont couponShow" v-if="is_couponShow">
			<div class="q-close" @click="couponShow">取消</div>
			<h1 class="center line40 bordB1 ftn">优惠券</h1>
			<div class="infor">
				<ul>
					<li v-for="(item,index) in couponData" :key="index"  @click="useCoupon(index)">
						<span>{{item.type==1?'抵扣券':'折扣券'}}{{item.type==2?item.discount/10+'折':item.value+'元'}}</span>
						<img class="setIcon" 
						:src="index==couponIndex?'../../static/images/coupons-icon.png':'../../static/images/coupons-icon1.png'" alt="">
					</li>
				</ul>
			</div>
			<div class="qusUnderFix flexRowBetween">
				<div class="left flex fs12">
					总价&nbsp;<em class="price ftw">{{pay.wxPay?pay.wxPay.price:'0.00'}}</em>
					<img class="arrowR" src="../../static/images/icon.png" >
				</div>
				<div class="right" @click="Utils.stopMultiClick(submit)">提交订单</div>
			</div>
		</div>
		
		<!-- 小费弹框 -->
		<div class="qjAlertCont tippingShow" v-if="is_tippingShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="tippingShow">取消</span>
				<span>小费</span>
				<span class="pucolor fs12" @click="confirmTip()">确定</span>
			</div>
			<div class="wpMsgBox pdlr4 mgt20">
				<div class="item flexRowBetween">
					<span v-for="(item,index) in tipDate" :key="index" :class="seltCurr == index?'on':''" 
					@click="seltSpecs(index)">{{item}}</span>
					<span><input placeholder="其他金额" v-model="tip" @focus="tipInput()"/></span>
				</div>
			</div>
		</div>
		
		<!-- 费用明细 -->
		<div class="qjAlertCont moneyMxShow" v-if="is_moneyMxShow" style="padding-bottom:60px;">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="moneyMxShow">关闭</span>
				<span>费用明细</span>
				<span class="flex">
					<a class="pucolor fs12" href="price_specs.html">价格规格</a>
					<img class="arrowR" src="../../static/images/icon.png" >
				</span>
			</div>
			<div class="pdlr4 list">
				<ul>
					<li class="flexRowBetween" v-for="(item,index) in moneyMxDate" :key='index'>
						<span>{{item.title}}<em class="fs12 color9">{{item.range}}</em></span>
						<span>{{item.price}}</span>
					</li>
				</ul>
			</div>
			<div class="qusUnderFix flexRowBetween">
				<div class="left flex fs12" @click="moneyMxShow">
					总价&nbsp;<em class="price ftw">{{pay.wxPay?pay.wxPay.price:'0.00'}}</em>
					<img class="arrowR" src="../../static/images/icon.png" >
				</div>
				<div class="right">提交订单</div>
			</div>
		</div>
	</div>
</template>

<script>
	import hTimePicker from "@/components/h-timePicker/h-timePicker.vue";
	export default {
		components: { hTimePicker },
		data() {
			return {
				
				Router:this.$Router,
				is_show:false,
				mainData: [],
				buyData:[

				],
				buyAddress:{},
				startAddress:{
					
				},
				pay:{
					coupon:[]
				},
				is_accessShow:false,
				seltData:0,
				setData:[
					{title:'今天',thatDay:'立即购买'},
					{title:'明天',thatDay:'23'}
				],
				seltCur:0,
				is_couponShow:false,
				is_tippingShow:false,
				tipDate:['2元','10元','15元','20元','25元'],
				seltCurr:0,
				is_moneyMxShow:false,
				moneyMxDate:[
					
				],
				num:1,
				is_dianfuShow:false,
				clickCur:-1,
				submitData:{
					passage1:'',//备注
					start_time:'立即购买',//取件时间,
					main_price:0,
					distance_price:0,
					weight_price:0,
					night_price:0,
					gratuity:0,
					insured_price:0,
					weather_price:0,
					holiday_price:0,
					rush_price:0,
					member_reduce:0,
					coupon_reduce:0,
					rider_income:0,
					platform_income:0,
					start_site:'',
					start_longitude:'',
					start_latitude:'',
					start_name:'',
					start_phone:'',
					end_site:'',
					end_longitude:'',
					end_latitude:'',
					end_name:'',
					end_phone:'',
					city_id:'',
					invalid_time:'',
					price:0,
					value:'',
					transport_status:1,
					total_distance:''
				},
				totalPrice:0,
				tip:'',
				couponData:[],
				distance:0,
				Utils:this.$Utils,
				chooseCoupon:[],
				couponIndex:-1,
				
			}
		},

		onLoad(options) {
			const self = this;
			self.clickCur = options.index;
			self.submitData.passage1 = options.text;
			self.submitData.city_id= uni.getStorageSync('city_id');
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			var res = self.$Token.getProjectToken(function() {
				self.$Utils.loadAll(['getBuyData'], self)
			});
			if (res) {
				self.$Utils.loadAll(['getBuyData'], self)
			};
			self.getUserCouponData()
			self.getMainData()
		},
		
		onShow() {
			const self = this;
			var now = Date.parse(new Date())/1000;
			self.submitData.invalid_time = now+3600;
			if(uni.getStorageSync('buyAddress')){
				self.buyAddress = uni.getStorageSync('buyAddress');
				self.submitData.end_site = self.buyAddress.city+self.buyAddress.detail;
				self.submitData.end_longitude = self.buyAddress.longitude;
				self.submitData.end_latitude = self.buyAddress.latitude;
				self.submitData.end_name = self.buyAddress.name;
				self.submitData.end_phone = self.buyAddress.phone;
			};
			self.countDistance()
		},

		methods: {
			
			changeTime(e){
				const self = this;
				self.submitData.start_time = e
				console.log(e)
			},
			
			getUserCouponData(isNew) {
				const self = this;
				if(isNew){
					self.couponData = []
				};
				var now = Date.parse(new Date());
				const postData = {};
				postData.tokenFuncName = 'getProjectToken';
				postData.searchItem = {
					use_step: 1,
					type: ['in', [1, 2]]
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.couponData.push.apply(self.couponData, res.info.data)
					}
					
					self.$Utils.finishFunc('getUserCouponData');
			
				};
				self.$apis.userCouponGet(postData, callback);
			},
			
			submit(){
				const self = this;
				uni.setStorageSync('canClick',false);
				if(self.num==1){
					if(!self.startAddress.name){
						uni.setStorageSync('canClick',true);
						self.$Utils.showToast('请选择购买地点', 'none');
						return;
					}
				};
				
				var data = self.$Utils.cloneForm(self.submitData);
				var orderList = [
					{product_id:self.buyData[self.clickCur].id,count:1,type:3,data:data}
				];
				self.addOrder(orderList)	
			},
			
			addOrder(orderList) {
				const self = this;	
				if(self.orderId){
					 self.payNow();
					 return
				};
				const postData = {}; 
				postData.orderList = self.$Utils.cloneForm(orderList);
				postData.tokenFuncName = 'getProjectToken';
				const callback = (res) => {
					if (res && res.solely_code == 100000) {
						self.orderId = res.info.id;
						self.payNow()
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
			
			
			
			payNow(order_id) {
				const self = this;	
				
				const postData = self.$Utils.cloneForm(self.pay);
				postData.tokenFuncName = 'getProjectToken',
				postData.searchItem = {
					id: self.orderId
				};	
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
										self.$Router.redirectTo({route:{path:'/pages/user_myOrder/user_myOrder'}})
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
								self.$Router.redirectTo({route:{path:'/pages/user_myOrder/user_myOrder'}})
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
			
			
			getBaiduDistance() {
				const self = this;
				
			},
			
			countDistance(){
				const self = this;
				if(self.buyAddress.latitude&&self.startAddress.latitude){
					const postData = {
						tokenFuncName:'getProjectToken',
						start_longitude:parseFloat(self.buyAddress.longitude),
						start_latitude:parseFloat(self.buyAddress.latitude),
						end_longitude:parseFloat(self.startAddress.longitude),
						end_latitude:parseFloat(self.startAddress.latitude)
					};
					
					const callback = (res) => {
						if(res.solely_code==100000){
							self.distance = parseFloat(res.info.distance/1000).toFixed(2);
							self.submitData.total_distance = self.distance;
							console.log('self.distance',self.distance)
						}else{
							self.$Utils.showToast(res.msg, 'none');
						}
						self.getMainData()
					};
					self.$apis.getBaiduDistance(postData, callback);
				}else{
					self.distance=0;
					self.getMainData()
				};
				console.log('self.distance',self.distance)
				
			},
			
			getMainData() {
				const self = this;
				var d = new Date();
				var currHours=d.getHours();
				console.log('currHours',currHours);
				const postData = {};
				postData.tokenFuncName = 'getProjectToken';
				postData.getAfter = {
					param1:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							distance:['<',self.distance],
							type:3,
							fee_type:1
						},
						condition:'=',
						order:{
							distance:'desc'
						}
					},
					param2:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							fee_type:2,
							type:3,
						},
						condition:'=',
					},
					param3:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							fee_type:3,
							is_use:1,
							type:3,
							start:['<=',currHours],
							end:['>=',currHours]
						},
						condition:'=',
					},
					param4:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							fee_type:4,
							is_use:1,
							type:3,
						},
						condition:'=',
					},
					param5:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							fee_type:5,
							is_use:1,
							type:3,
						},
						condition:'=',
					},
					param6:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							fee_type:6,
							is_use:1,
							type:3,
						},
						condition:'=',
					},
				};
				if(self.distance>0){
					postData.getAfter.param1={
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							distance:['<',self.distance],
							type:3,
							fee_type:1
						},
						condition:'=',
						order:{
							distance:'desc'
						}
					}
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData = res.info.data[0];
					};
					self.countPrice();
				};
				self.$apis.userInfoGet(postData, callback);
			},
			
			useCoupon(index) {
				const self = this;	
				console.log(index)
				var id = self.couponData[index].id;
				self.couponIndex = -1;
				var findCoupon = self.$Utils.findItemInArray(self.couponData, 'id', id);
				var findItem = self.$Utils.findItemInArray(self.pay.coupon, 'id', id);
				console.log('findCoupon', findCoupon)
				self.is_couponShow = false;
				self.is_show = false;
				if(self.pay.coupon.length>=1){
					self.pay.coupon = []
					self.chooseCoupon = []
				};
				if (findCoupon) {
					findCoupon = findCoupon[1];
					var findSameCoupon =  self.$Utils.findItemsInArray(self.pay.coupon, 'product_id', id);
				} else {
					self.$Utils.showToast('优惠券错误', 'none');
					return;
				};
				if (findItem) {
					self.pay.coupon.splice(findItem[0], 1);
					self.chooseCoupon = []
				} else {
					console.log('self.data.price - self.data.couponTotalPrice',self.totalPrice - self.couponTotalPrice);
					console.log('findCoupon.condition',findCoupon.condition);
					if ((self.totalPrice - self.couponTotalPrice) < findCoupon.condition) {
						self.$Utils.showToast('未达满减标准', 'none');				
						return;
					};			
					console.log('findSameCoupon.length', findSameCoupon.length)
					if (self.pay.coupon.length >= 1) {
						self.$Utils.showToast('叠加使用超限', 'none');
					
						return;
					};
					if (findCoupon.type == 1) {
						var couponPrice = findCoupon.value;
						console.log('findCoupon.discount', findCoupon.discount)
					} else if (findCoupon.type == 2) {
						
						var couponPrice = parseFloat(self.totalPrice).toFixed(2) - parseFloat(findCoupon.discount / 100 * self.totalPrice)
							.toFixed(2);
					};
					if (parseFloat(couponPrice) + parseFloat(self.couponTotalPrice) > parseFloat(self.totalPrice)) {
						couponPrice = parseFloat(self.totalPrice).toFixed(2) - parseFloat(self.couponTotalPrice).toFixed(2);
					};
					self.pay.coupon.push({
						id: id,
						price: parseFloat(couponPrice).toFixed(2)
					});
					self.chooseCoupon.push({
						id: id,
						price: couponPrice,
					});
					self.is_couponShow = false;
					self.is_show = false;
					self.couponIndex = index;
				};
				self.countPrice();
			},
			
			countPrice(){
				const self = this;
				var nowTime = Date.parse(new Date());
				self.totalPrice = 0;
				self.submitData.distance_price=0;
				self.submitData.weight_price=0;
				self.submitData.night_price=0;
				self.submitData.gratuity=0;
				self.submitData.insured_price=0;
				self.submitData.weather_price=0;
				self.submitData.holiday_price=0;
				self.submitData.rush_price=0;
				self.submitData.member_reduce=0;
				self.submitData.coupon_reduce=0;
				self.submitData.rider_income=0;
				self.submitData.platform_income=0;
				self.moneyMxDate = [];
				self.totalPrice = parseFloat(self.buyData[self.clickCur].price);
				self.submitData.main_price = self.totalPrice;
				self.moneyMxDate.push({title:'基础配送费',price:'￥'+self.submitData.main_price});
				self.couponTotalPrice = self.$Utils.addItemInArray(self.pay.coupon, 'price');
				if(self.mainData.param1&&self.mainData.param1.length>0&&self.distance>0){
					for (var i = 0; i < self.mainData.param1.length; i++) {
						if(i==0){
							self.submitData.distance_price = (self.distance-self.mainData.param1[i].distance)*self.mainData.param1[i].price
						}else{
							self.submitData.distance_price = (self.mainData.param1[i-1].distance-self.mainData.param1[i].distance)*self.mainData.param1[i].price+ self.submitData.distance_price
						}
						console.log('self.totalPrice',self.totalPrice)
					};
					self.submitData.distance_price = parseFloat(self.submitData.distance_price).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.distance_price)).toFixed(2);
					self.moneyMxDate.push({title:'距离附加',range:self.distance+'公里',price:'￥'+self.submitData.distance_price})
				};
				/* if(self.mainData.param2.length>0){
					for (var i = 0; i < self.mainData.param2.length; i++) {
						if(i==0){
							self.submitData.weight_price = (self.weight-self.mainData.param2[i].weight)*self.mainData.param2[i].price
						}else{
							self.submitData.weight_price = (self.mainData.param2[i-1].weight-self.mainData.param2[i].weight)*self.mainData.param2[i].price+ self.submitData.weight_price
						}
						console.log('self.totalPrice',self.totalPrice)
					};
					self.submitData.weight_price = parseFloat(self.submitData.weight_price).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.weight_price)).toFixed(2);
					self.moneyMxDate.push({title:'重量附加',range:self.weight +'公斤',price:'￥'+self.submitData.weight_price})
				}; */
				if(self.mainData.param3.length>0){
					
					self.submitData.night_price = parseFloat(self.mainData.param3[0].price).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.mainData.param3[0].price)).toFixed(2);
					self.moneyMxDate.push({title:'夜间配送费',price:'￥'+self.submitData.night_price})
				};
				var money = parseFloat(self.submitData.main_price) + parseFloat(self.submitData.distance_price) 
				+ parseFloat(self.submitData.night_price) + parseFloat(self.submitData.weight_price);
				
				console.log('money',money);
				if(self.mainData.param4.length>0){
					
					self.submitData.weather_price = (money*(self.mainData.param4[0].rate/100)).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.weather_price)).toFixed(2);
					self.moneyMxDate.push({title:'恶劣天气附加费',price:'￥'+self.submitData.weather_price})
				};
				if(self.mainData.param5.length>0){
					
					self.submitData.holiday_price = (money*(self.mainData.param5[0].rate/100)).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.holiday_price)).toFixed(2);
					self.moneyMxDate.push({title:'节假日附加费',price:'￥'+self.submitData.holiday_price})
				};
				if(self.mainData.param6.length>0){
					
					self.submitData.rush_price = (money*(self.mainData.param6[0].rate/100)).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.rush_price)).toFixed(2);
					self.moneyMxDate.push({title:'高峰时段附加费',price:'￥'+self.submitData.rush_price})
				};
				if(self.bjFee>0){
					self.submitData.insured_price = self.bjFee;
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.insured_price)).toFixed(2);
					self.moneyMxDate.push({title:'保价费',price:'￥'+self.submitData.insured_price})
				};
				if(self.tip>0){
					self.submitData.gratuity = self.tip;
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.gratuity)).toFixed(2);
					self.moneyMxDate.push({title:'小费',price:'￥'+self.submitData.gratuity})
				};
				if(self.mainData.member_time>nowTime){
					var ratio = parseFloat(uni.getStorageSync('user_info').thirdApp.custom_rule.member);
					var memberMoney = parseFloat(self.submitData.main_price)+parseFloat(self.submitData.weight_price);
					self.submitData.member_reduce = (memberMoney*(ratio/100)).toFixed(2);
					self.moneyMxDate.push({title:'会员抵扣',price:'￥'+self.submitData.member_reduce})
				};
				if(self.couponTotalPrice>0){
					self.submitData.coupon_reduce = self.couponTotalPrice;
					
					self.moneyMxDate.push({title:'优惠券抵扣',price:'￥'+self.submitData.coupon_reduce})
				};
				
				self.submitData.platform_income = ((parseFloat(self.totalPrice)-parseFloat(self.submitData.member_reduce))*(parseFloat(uni.getStorageSync('user_info').thirdApp.custom_rule.tax)/100) - parseFloat(self.couponTotalPrice)).toFixed(2);
				console.log('parseFloat(self.totalPrice)',parseFloat(self.totalPrice));
				console.log(parseFloat(uni.getStorageSync('user_info').thirdApp.custom_rule.tax)/100)
				console.log(self.submitData.member_reduce)
				
				var wxPay = parseFloat(self.totalPrice) - parseFloat(self.couponTotalPrice) - parseFloat(self.submitData.member_reduce);
				self.submitData.price = (parseFloat(wxPay)+parseFloat(self.couponTotalPrice)).toFixed(2);
				self.submitData.rider_income = (parseFloat(wxPay)  - parseFloat(self.submitData.platform_income)).toFixed(2);
				if (wxPay > 0) {
					self.pay.wxPay = {
						price: wxPay.toFixed(2),
					};
				} else {
					  delete self.pay.wxPay;	 
				};
				//self.submitData.rider_income = (wxPay.toFixed(2) +  parseFloat(self.couponTotalPrice) - parseFloat(self.submitData.platform_income)).toFixed(2);
				
			},
			
			chooseAddress(e) {
				const self = this;
				uni.authorize({
				    scope: 'scope.userLocation',
				    success() {
				        uni.chooseLocation({
				        	success: (res) => {
				        		console.log(res)
				        		self.startAddress = res;
				        		self.submitData.start_site = self.startAddress.name;
				        		
								var newObject = self.$Utils.qqMapTransBMap(self.startAddress.longitude,self.startAddress.latitude)
															
								self.submitData.start_longitude = newObject.lng;
								self.submitData.start_latitude  = newObject.lat;
								console.log(self.submitData)
								console.log('self.startAddress',self.startAddress)
				        	},
				        	fail: (e) => {
				        		uni.getSetting({
				        			success: (res) => {
				        				console.log(res)
				        				let locaAuth = res.authSetting['scope.userLocation']
				        				if (locaAuth) {/* 判断位置是否已经授权，是选择地图位置点击取消触发的fail，再选择位置 */
				        					console.log('地图点击取消')
				        					uni.chooseLocation({
				        						success: (res) => {
				        							self.startAddress = res;
													self.submitData.start_site = self.startAddress.name;
													var newObject = self.$Utils.qqMapTransBMap(self.startAddress.longitude,self.startAddress.latitude)
																				
													self.submitData.start_longitude = newObject.lng;
													self.submitData.start_latitude  = newObject.lat;
				        						},
				        					});
				        				}
				        				if (!locaAuth) { /* 如果地理位置没授权 */
				        					console.log(222)
				        					uni.showModal({
				        					    title: '提示',
				        					    content: '需要授权位置信息',
				        						confirmColor:'#ca1c1d',
				        						showCancel:true,
				        					    success: function (res) {
				        					        if (res.confirm) {
				        					            uni.openSetting({
				        					            	success: (res) => {
				        					            		console.log(res.authSetting)
				        					            	},
				        					            	fail: (res) => {
				        					            		console.log(res)
				        					            	},
				        					            });
				        					        } else if (res.cancel) {
				        					           
				        					        }
				        					    }
				        					});			
				        					
				        				
				        				}
				        			}
				        		})
				        	}
				        });
				    },
					fail: (e) => {
						uni.showModal({
						    title: '提示',
						    content: '需要授权位置信息',
							confirmColor:'#ca1c1d',
							showCancel:true,
						    success: function (res) {
						        if (res.confirm) {
						            uni.openSetting({
						            	success: (res) => {
						            		console.log(res.authSetting)
						            	},
						            	fail: (res) => {
						            		console.log(res)
						            	},
						            });
						        } else if (res.cancel) {
						           
						        }
						    }
						});
					}
				})
				console.log('self.submitData',self.submitData)
			},
			
			getBuyData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					city_id:uni.getStorageSync('city_id')
				};
				postData.getBefore = {
					caseData: {
						tableName: 'Label',
						searchItem: {
							title: ['=', ['代买']],
						},
						middleKey: 'category_id',
						key: 'id',
						condition: 'in',
					},
				};
				postData.order = {
					create_time:'asc'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.buyData.push.apply(self.buyData, res.info.data);
						
					}
					console.log('self.buyData', self.buyData)
					self.$Utils.finishFunc('getBuyData');
				};
				self.$apis.productGet(postData, callback);
			},
			
			confirmTip(){
				const self = this;
				self.tippingShow();
				self.countPrice()
			},
			
			tipInput(event){
				const self = this;
				self.seltCurr = -1;
			},
						
			clickPro(index){
				const self = this;
				self.clickCur = index
			},
			// 起始时间弹框
			accessShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_accessShow = !self.is_accessShow
			},
			// 时间日期选择
			qjSetData(index){
				const self = this;
				self.seltData = index
			},
			
			// 优惠券
			seltCoupon(index){
				const self = this;
				self.seltCur = index
			},
			couponShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_couponShow = !self.is_couponShow
			},
			
			// 小费弹框
			tippingShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_tippingShow = !self.is_tippingShow
			},
			
			seltSpecs(index){
				const self = this;
				self.seltCurr=index;
				self.tip = parseInt(self.tipDate[self.seltCurr]);
				console.log('self.tip',self.tip)
			},
			
			// 价格明细
			moneyMxShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_moneyMxShow = !self.is_moneyMxShow
			},
			seltChage(num){
				const self = this;
				if(num!=self.num){
					self.num = num;
					if(self.num==2){
						self.startAddress = {};
						self.submitData.start_site = '';
						self.submitData.start_latitude = '';
						self.submitData.start_longitude = '';
						self.countDistance()
					}
				}
			},
			// 垫付费用弹框
			dianfuShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_dianfuShow = !self.is_dianfuShow
			},
			
			
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/page.css";
</style>
