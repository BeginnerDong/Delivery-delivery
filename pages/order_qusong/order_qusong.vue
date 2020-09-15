<template>
	<div style="padding-bottom: 90rpx;">
		<div class="">
			<a class="lineMsg flexRowBetween" @click="back()">
				<div>{{goodsInfo.title}}、{{goodsInfo.value}}、{{goodsInfo.weight}}公斤</div>
				<img class="arrowR" src="../../static/images/icon.png" >
			</a>
		</div>
		<div class="f5H10"></div>
		
		<div class="pr">
			<div class="flexRowBetween qsCotMsg"  @click="Router.navigateTo({route:{path:'/pages/address/address?name=from'}})">
				<div class="left"><span class="note qu">取</span></div>
				<div class="flexRowBetween cont bordB1" v-if="fromAddress.city">
					<div class="infor">
						<h1 class="ftn fs14">{{fromAddress.city}}</h1>
						<p class="fs12 color9">{{fromAddress.detail}}</p>
					</div>
					<div class="phone">
						<p>{{fromAddress.phone}}</p>
						<img class="arrowR" src="../../static/images/icon.png" alt="">
					</div>
				</div>
				<div class="flexRowBetween cont bordB1" v-else>
					请选择或添加地址
					<div class="phone">
						<img class="arrowR" src="../../static/images/icon.png" alt="">
					</div>
				</div>
			</div>
			<img class="dian3" src="../../static/images/dian3.png">
			<div class="flexRowBetween qsCotMsg" @click="Router.navigateTo({route:{path:'/pages/address/address?name=go'}})">
				<div class="left"><span class="note shou">收</span></div>
				<div class="flexRowBetween cont" v-if="goAddress.city">
					<div class="infor">
						<h1 class="ftn fs14">{{goAddress.city}}</h1>
						<p class="fs12 color9">{{goAddress.detail}}</p>
					</div>
					<div class="phone">
						<p>{{goAddress.phone}}</p>
						<img class="arrowR" src="../../static/images/icon.png" alt="">
					</div>
				</div>
				<div class="flexRowBetween cont" v-else>
					请选择或添加地址
					<div class="phone">
						<img class="arrowR" src="../../static/images/icon.png" alt="">
					</div>
				</div>
			</div>
		</div>
		<div class="f5H10"></div>
		
		<div class="qsLineinfor">
			<ul>
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
					<div class="left">备注：</div>
					<div class="right">
						<input type="text" style="color: #000000;" placeholder-style="color:#999"  v-model="submitData.passage1" placeholder="物品描述或送件要求" />
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">物品保价：</div>
					<div class="right"  @click="baojiaShow" :style="submitData.insured_price>0?'color:#000000':''">
						{{submitData.insured_price>0?submitData.insured_price+'元':'选择保价，安心配送'}}<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">小费：</div>
					<div class="right"  @click="tippingShow" :style="submitData.gratuity>0?'color:#000000':''">
						{{submitData.gratuity>0?submitData.gratuity+'元':'给小费抢单更快哟'}}<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">取件时间：</div>
					<!-- <div class="right" @click="accessShow">
						<span class="fs14 color3">立即取件</span><img class="arrowR" src="../../static/images/icon.png" >
					</div> -->
					<view class="flex">
						<hTimePicker sTime="0" cTime="24" interval="15" startValue="立即取件" @changeTime="changeTime">
						  <view slot="pCon" class="changeTime">
						    {{submitData.start_time}}
						  </view>
						</hTimePicker>
						<img class="arrowR" src="../../static/images/icon.png" >
					</view>
					
				</li>
				<li class="flexRowBetween">
					<div class="left">预计送达时间：</div>
					<div class="right">
						<span class="red" v-if="distance>0">取送距离{{distance}}km，预计{{time}}分钟送达</span>
						<span class="red" v-else>无法估算</span>
					</div>
				</li>
			</ul>
		</div>
		
		<div class="qusUnderFix flexRowBetween">
			<div class="left flex fs12" @click="moneyMxShow">
				总价&nbsp;<em class="price ftw">{{pay.wxPay?pay.wxPay.price:'0.00'}}</em>
				<img class="arrowR" src="../../static/images/icon.png" >
			</div>
			<div class="right"  @click="submit">提交订单</div>
		</div>
		
		<!-- 弹框背景 -->
		<div class="black-bj" v-show="is_show"></div>
		
		<!-- 优惠券 -->
		<div class="qjAlertCont couponShow" v-show="is_couponShow">
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
				<div class="right" @click="submit">提交订单</div>
			</div>
		</div>
			
		<!-- 物品保价 -->
		<div class="qjAlertCont baojiaShow" v-show="is_baojiaShow">
			<div class="q-close" @click="baojiaShow">关闭</div>
			<p class="center line40 bordB1">物品保价</p>
			<div class="pdlr4">
				<h1 class="pdt10 pdb20">请输入物品实际价值</h1>
				<div class="edit bordB1">
					<input type="number" v-model="value" @input="baojia"/>
				</div>
				<p class="pdtb10 fs12 color6">保价费用：<em class="pucolor">{{bjFee}}元</em></p>
				<div class="flex fs12 color9">
					<img class="setIcon" v-if="isAgree" src="../../static/images/coupons-icon.png" @click="agreeSelt"/>
					<img class="setIcon" v-if="!isAgree" src="../../static/images/coupons-icon1.png" @click="agreeSelt"/>
					<label>已阅读并同意</label>
					<a class="pucolor" @click="Router.navigateTo({route:{path:'/pages/baojia_treaty/baojia_treaty'}})">《保价协议》</a>
				</div>
				<div class="submitbtn mgt20" >
					<div class="btn" @click="confirmBj()">确定</div>
				</div>
			</div>
		</div>
		
		<!-- 小费弹框 -->
		<div class="qjAlertCont tippingShow" v-show="is_tippingShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="tippingShow">取消</span>
				<span>小费</span>
				<span class="pucolor fs12" @click="confirmTip()">确定</span>
			</div>
			<div class="wpMsgBox pdlr4 mgt20">
				<div class="item flexRowBetween">
					<span v-for="(item,index) in tipDate" :key="index" :class="seltCurr == index?'on':''" 
					@click="seltSpecs(index)">{{item.name}}</span>
					<span><input style="height: 28px;" placeholder="其他金额" v-model="tip" @focus="tipInput"/></span>
				</div>
			</div>
		</div>
		
		<!-- 取件时间弹框 -->
		<div class="qjAlertCont accessShow" style="padding-bottom: 80px;" v-show="is_accessShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="accessShow">取消</span>
				<span>取件时间</span>
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
		
		<!-- 费用明细 -->
		<div class="qjAlertCont moneyMxShow" v-show="is_moneyMxShow" style="padding-bottom:60px;">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="moneyMxShow">关闭</span>
				<span>费用明细</span>
				<span class="flex">
					<a class="pucolor fs12"  @click="Router.navigateTo({route:{path:'/pages/price_specs/price_specs?type=view'}})">价格规格</a>
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
				Utils:this.$Utils,
				mainData: [],
				is_show:false,
				is_couponShow:false,
				isAgree:false,
				is_baojiaShow:false,
				is_tippingShow:false,
				is_accessShow:false,
				is_moneyMxShow:false,
				seltCurr:0,
				seltCur:0,
				seltData:0,
				
				tipDate:[{name:'不加了',value:0},{name:'2元',value:2},{name:'5元',value:5},
				{name:'10元',value:10},{name:'15元',value:15},{name:'20元',value:20}],
				setData:[
					{title:'今天',thatDay:'立即取件'},
					{title:'明天',thatDay:'23'},
					{title:'后天',thatDay:'24'}
				],
				moneyMxDate:[
					/* 
					{title:'距离附加',range:'2.9公里',price:'￥2.5'},
					{title:'重量附加',range:'6.0公斤',price:'￥6'},
					{title:'小费',price:'￥5'} */
				],
				pay:{
					coupon:[]
				},
				submitData:{
					passage1:'',//备注
					start_time:'立即取件',//取件时间,
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
					weight:'',
					value:'',
					transport_status:1,
					total_distance :''
				},
				fromAddress:{},
				goAddress:{},
				goodsInfo:{},
                distance:0,	
				totalPrice:0,
				bjFee:0,
				tip:'',
				couponData:[],
				time:'',
				chooseCoupon:[],
				couponIndex:-1
			}
		},

		onLoad() {
			const self = this;
			
			self.submitData.city_id= uni.getStorageSync('city_id')
			self.getMainData()
		},
		
		onShow() {
			const self = this;
			if(!uni.getStorageSync('target')){
				var now = Date.parse(new Date())/1000;
				self.submitData.invalid_time = now+3600;
				if(uni.getStorageSync('fromAddress')){
					
					self.fromAddress = uni.getStorageSync('fromAddress');
					self.submitData.start_site = self.fromAddress.city+self.fromAddress.detail;
					self.submitData.start_latitude = self.fromAddress.latitude;
					self.submitData.start_longitude = self.fromAddress.longitude;
					self.submitData.start_name = self.fromAddress.name;
					self.submitData.start_phone = self.fromAddress.phone;
				};
				if(uni.getStorageSync('goAddress')){
					self.goAddress = uni.getStorageSync('goAddress');
					self.submitData.end_site = self.goAddress.city+self.goAddress.detail;
					self.submitData.end_latitude = self.goAddress.latitude;
					self.submitData.end_longitude = self.goAddress.longitude;
					self.submitData.end_name = self.goAddress.name;
					self.submitData.end_phone = self.goAddress.phone;
				};
				if(uni.getStorageSync('goodsInfo')){
					self.goodsInfo = uni.getStorageSync('goodsInfo');
					self.submitData.weight = self.goodsInfo.weight;
					self.submitData.value = self.goodsInfo.value;
				};
				self.totalPrice = parseFloat(self.goodsInfo.price);
				console.log('self.totalPrice',self.totalPrice)
				self.submitData.main_price = self.totalPrice;
				self.moneyMxDate = [];
				self.moneyMxDate.push({title:'基础配送费',price:'￥'+self.submitData.main_price});
				if(self.fromAddress.latitude&&self.goAddress.latitude){
					self.getBaiduDistance()
					
				}
				self.getUserCouponData(true)
			}else{
				uni.removeStorageSync('target')
			}
			
		},

		methods: {
			
			back(){
				const self = this;
				uni.navigateBack({
					delta:1
				})
			},
			
			changeTime(e){
				const self = this;
				self.submitData.start_time = e
				console.log(e)
			},
			
			getBaiduDistance() {
				const self = this;
				const postData = {
					tokenFuncName:'getProjectToken',
					start_longitude:parseFloat(self.fromAddress.longitude),
					start_latitude:parseFloat(self.fromAddress.latitude),
					end_longitude:parseFloat(self.goAddress.longitude),
					end_latitude:parseFloat(self.goAddress.latitude)
				};
				const callback = (res) => {
					if(res.solely_code==100000){
						self.distance = parseFloat(res.info.distance/1000).toFixed(2);
						self.submitData.total_distance = self.distance;
						self.time = parseInt(self.distance*uni.getStorageSync('user_info').thirdApp.custom_rule.time)+parseInt(uni.getStorageSync('user_info').thirdApp.custom_rule.basic_time);
						var res = self.$Token.getProjectToken(function() {
							self.$Utils.loadAll(['getMainData'], self)
						});
						if (res) {
							self.$Utils.loadAll(['getMainData'], self)
						};
					}else{
						self.$Utils.showToast(res.msg, 'none');
					}
				};
				self.$apis.getBaiduDistance(postData, callback);
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
				if(JSON.stringify(self.fromAddress)=="{}"){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请选择起始地点', 'none');
					return;
				};
				
				if(JSON.stringify(self.goAddress)=="{}"){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请选择终点地点', 'none');
					return;
				};
				var data = self.$Utils.cloneForm(self.submitData);
				var orderList = [
					{product_id:self.goodsInfo.id,count:1,type:1,data:data}
				];
				console.log('data',data)
				console.log('pay',self.pay)
				//return
				self.addOrder(orderList)	
			},
			
			addOrder(orderList) {
				const self = this;	
				/* if(self.orderId){
					 self.payNow();
					 return
				}; */
				const postData = {}; 
				postData.orderList = self.$Utils.cloneForm(orderList);
				if(!wx.getStorageSync('user_info')||wx.getStorageSync('user_info').headImgUrl==''||!wx.getStorageSync('user_info').headImgUrl){
				  postData.refreshToken = true;
				};
				postData.tokenFuncName = 'getProjectToken';
				const callback = (res) => {
					if (res && res.solely_code == 100000) {
						self.orderId = res.info.id;
						uni.removeStorageSync('fromAddress');
						uni.removeStorageSync('goAddress');
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
			
			
			
			
			baojia(event){
				const self = this;
				console.log('event.target.value',event.target.value);
				self.value = 
				self.bjFee = (event.target.value*(uni.getStorageSync('user_info').thirdApp.custom_rule.insured_rate/100)).toFixed(2)
			},
			
			confirmBj(){
				const self = this;
				if(!self.isAgree){
					self.$Utils.showToast('请阅读协议并同意', 'none');
					return
				};
				self.baojiaShow();
				self.countPrice()
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
			
			getMainData() {
				const self = this;
				const postData = {};
				var d = new Date();
				var currHours=d.getHours();
				console.log('currHours',currHours);
				postData.tokenFuncName = 'getProjectToken';
				postData.getAfter = {
					param1:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							distance:['<',self.distance],
							type:1,
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
							weight:['<',self.submitData.weight],
							fee_type:2,
							type:1,
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
							start:['<',currHours],
							end:['>',currHours],
							type:1,
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
							type:1,
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
							type:1,
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
							type:1,
						},
						condition:'=',
					},
				}
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData = res.info.data[0];
					};
					self.countPrice();
					self.$Utils.finishFunc('getMainData');
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
					console.log('couponPrice',couponPrice)
					self.pay.coupon.push({
						id: id,
						price: parseFloat(couponPrice).toFixed(2)
					});
					self.chooseCoupon.push({
						id: id,
						price: couponPrice,
					});
					self.is_couponShow = false
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
				self.totalPrice = parseFloat(self.goodsInfo.price);
				self.moneyMxDate.push({title:'基础配送费',price:'￥'+self.submitData.main_price});
				self.couponTotalPrice = self.$Utils.addItemInArray(self.pay.coupon, 'price');
				if(self.mainData.param1.length>0){
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
				if(self.mainData.param2.length>0){
					for (var i = 0; i < self.mainData.param2.length; i++) {
						if(i==0){
							self.submitData.weight_price = (self.submitData.weight-self.mainData.param2[i].weight)*self.mainData.param2[i].price
						}else{
							self.submitData.weight_price = (self.mainData.param2[i-1].weight-self.mainData.param2[i].weight)*self.mainData.param2[i].price+ self.submitData.weight_price
						}
						console.log('self.totalPrice',self.totalPrice)
					};
					self.submitData.weight_price = parseFloat(self.submitData.weight_price).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.weight_price)).toFixed(2);
					self.moneyMxDate.push({title:'重量附加',range:self.submitData.weight +'公斤',price:'￥'+self.submitData.weight_price})
				};
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
					self.moneyMxDate.push({title:'会员抵扣',price:'-￥'+self.submitData.member_reduce})
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
				console.log('wxPay',wxPay)
				if (wxPay > 0) {
					self.pay.wxPay = {
						price: wxPay.toFixed(2),
					};
				} else {
					  delete self.pay.wxPay;	 
				};
				
			},
			
			
			couponShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_couponShow = !self.is_couponShow
			},
			// 保价
			baojiaShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_baojiaShow = !self.is_baojiaShow
			},
			// 同意保价
			agreeSelt(){
				const self = this;
				self.isAgree = !self.isAgree
			},
			
			// 小费弹框
			tippingShow(){
				const self = this;
				
				self.is_show = !self.is_show
				self.is_tippingShow = !self.is_tippingShow
				/* if(self.seltCurr==-1){
					self.seltCurr=0;
					self.tip = self.tipDate[0].value;
				} */
				
			},
			
			seltSpecs(index){
				const self = this;
				self.seltCurr=index;
				self.tip =self.tipDate[self.seltCurr].value;
				console.log('self.tip',self.tip)
			},
			
			
			// 取件时间弹框
			accessShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_accessShow = !self.is_accessShow
			},
			// 取件时间日期选择
			qjSetData(index){
				const self = this;
				self.seltData = index
			},
			// 价格明细
			moneyMxShow(){
				const self = this;
				self.is_show = !self.is_show
				self.is_moneyMxShow = !self.is_moneyMxShow
			},	
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/page.css";
</style>