<template>
	<div style="padding-bottom: 50px;">
		<div class="agentMsg pdlr4" >
			<div class="daibanNav" style="margin-bottom: 10px;">
				<ul>
					<li :class="clickCur==index?'on':''" v-for="(item,index) in handleData"  :key="index" 
					@click="clickPro(index)">
						<img class="icon" :src="item.mainImg&&item.mainImg[0]?item.mainImg[0].url:''">
						<span>{{item.title}}</span>
					</li>
				</ul>
			</div>
		</div>
		<div class="f5H10"></div>
		<div class="agentMsg pdlr4" >
			<div style="padding-bottom: 10px;">代办业务需求说明栏</div>
			<div class="editTex">
				<textarea rows="" cols="4" v-model="submitData.passage1" style="font-size: 14px;"></textarea>
			</div>
		</div>
		<div class="bordB1"></div>
		<div class="qsLineinfor">
			<ul>
				<li class="flexRowBetween liContbox2">
					<div class="left">地址：</div>
					<div class="right" style="padding-bottom:0;" @click="Router.navigateTo({route:{path:'/pages/address/address?name=handle'}})">
						<input type="text" style="color: #000000;" v-model="submitData.start_site" name="" disabled="true" value="" placeholder="请选择业务办理地址" />
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</li>
				<li class="flexRowBetween liContbox2" style="paddng: 0 4%;" v-if="submitData.start_name!=''&&submitData.start_phone!=''">
					<div class="left" style="color: #999;font-size: 13px;">
						<input type="text" style="color: #000000;" v-model="submitData.start_name"  disabled="true" value="" />
					</div>
					<div class="right" style="font-size: 13px;">
						<input type="text" style="color: #000000;" v-model="submitData.start_phone"  disabled="true" value="" />
					</div>
				</li>
				
				<div class="f5H10"></div>
				<li class="flexRowBetween">
					<div class="left">起始时间：</div>
					<!-- <div class="right" @click="accessShow">
						<span class="fs14 color3">立即开始</span><img class="arrowR" src="../../static/images/icon.png" >
					</div> -->
					
					<view class="flex">
						<hTimePicker sTime="0" cTime="24" interval="15" startValue="立即开始" @changeTime="changeTime">
						  <view slot="pCon" class="changeTime">
						    {{submitData.start_time}}
						  </view>
						</hTimePicker>
						<img class="arrowR" src="../../static/images/icon.png" >
					</view>
				</li>
				<li class="flexRowBetween">
					<div class="left">预约时长：</div>
					<div class="right">
						<input type="number" @blur="getMainData" v-model="time"  placeholder="请输入时长(分钟)"/>
					</div>
				</li>
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
		<div class="black-bj" v-show="is_show"></div>
		
		<!-- 开始时间弹框 -->
		<div class="qjAlertCont accessShow" style="padding-bottom: 80px;" v-show="is_accessShow">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="accessShow">取消</span>
				<span>起始时间</span>
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
				<div class="right" @click="Utils.stopMultiClick(submit)">提交订单</div>
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
					<span><input style="height: 28px;" placeholder="其他金额" v-model="tip" @focus="tipInput()"/></span>
				</div>
			</div>
		</div>
		
		<!-- 费用明细 -->
		<div class="qjAlertCont moneyMxShow" v-show="is_moneyMxShow" style="padding-bottom:60px;">
			<div class="flexRowBetween pdlr4 line40 bordB1">
				<span class="fs12 color6" @click="moneyMxShow">关闭</span>
				<span>费用明细</span>
				<span class="flex">
					<a class="pucolor fs12" @click="Router.navigateTo({route:{path:'/pages/price_specs/price_specs'}})">价格规格</a>
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
				is_show:false,
				mainData: [],
				clickCur:0,
				handleData:[],
				is_accessShow:false,
				seltData:0,
				setData:[
					{title:'今天',thatDay:'立即开始'},
					{title:'明天',thatDay:'23'},
					{title:'后天',thatDay:'24'}
				],
				seltCur:0,
				is_couponShow:false,
				
				is_tippingShow:false,
				tipDate:[{name:'不加了',value:0},{name:'2元',value:2},{name:'5元',value:5},
				{name:'10元',value:10},{name:'15元',value:15},{name:'20元',value:20}],
				seltCurr:0,
				is_moneyMxShow:false,
				moneyMxDate:[
					
				],
				submitData:{
					passage1:'',//备注
					start_time:'立即开始',//取件时间,
					main_price:0,
					time_price:0,
					coupon_reduce:0,
					rider_income:0,
					platform_income:0,
					start_site:'',
					start_longitude:'',
					start_latitude:'',
					start_name:'',
					start_phone:'',
					city_id:'',
					invalid_time:'',
					price:0,
					transport_status:1,
					//total_distance:''
				},
				time:'',
				totalPrice:0,
				tip:'',
				couponData:[],
				Utils:this.$Utils,
				Router:this.$Router,
				pay:{
					coupon:[]
				},
				chooseCoupon:[],
				couponIndex:-1
			}
		},

		onLoad(options) {
			const self = this;
			var now = Date.parse(new Date())/1000;
			self.submitData.invalid_time = now+3600;
			if(options.index){
				self.clickCur = options.index;
			}
			
			self.submitData.passage1 = options.text;
			self.submitData.city_id= uni.getStorageSync('city_id');
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			var res = self.$Token.getProjectToken(function() {
				self.$Utils.loadAll(['getHandleData'], self)
			});
			if (res) {
				self.$Utils.loadAll(['getHandleData'], self)
			};
			self.getUserCouponData()
		},
		
		onShow() {
			const self = this;
			if(uni.getStorageSync('handleAddress')){
				self.handleAddress =  uni.getStorageSync('handleAddress');
				self.submitData.start_site = self.handleAddress.city+self.handleAddress.detail;
				self.submitData.start_longitude = self.handleAddress.longitude;
				self.submitData.start_latitude = self.handleAddress.latitude;
				self.submitData.start_name = self.handleAddress.name;
				self.submitData.start_phone = self.handleAddress.phone;
			};
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
			
			submit(){
				const self = this;
				uni.setStorageSync('canClick',false);
				
				if(self.submitData.start_site==''){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请选择业务办理地', 'none');
					return;
				}
				
				if(self.time==''||parseInt(self.time)<=0){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请输入正确的预约时长', 'none');
					return;
				};
				
				var data = self.$Utils.cloneForm(self.submitData);
				var orderList = [
					{product_id:self.handleData[self.clickCur].id,count:1,type:2,data:data}
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
						uni.removeStorageSync('handleAddress');
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
			
			getMainData() {
				const self = this;
				const postData = {};
				postData.tokenFuncName = 'getProjectToken';
				postData.getAfter = {

				};
				if(parseInt(self.time)>parseInt(self.handleData[self.clickCur].passage1)){
					postData.getAfter.param1={
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							//duration:['<',parseInt(self.time)],
							type:2,
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
			
			chooseAddress(e) {
				const self = this;
				uni.authorize({
				    scope: 'scope.userLocation',
				    success() {
				        uni.chooseLocation({
				        	success: (res) => {
				        		console.log(res)
				        		self.submitData.start_site = res.name;
				        		self.submitData.start_latitude = res.latitude;
								self.submitData.start_longitude = res.longitude;
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
				        							self.submitData.start_site = res.name;
				        							self.submitData.start_latitude = res.latitude;
				        							self.submitData.start_longitude = res.longitude;
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
				
			},
			
			getHandleData() {
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
							title: ['=', ['代办']],
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
						self.handleData.push.apply(self.handleData, res.info.data);
						self.countPrice()
					}
					console.log('self.handleData', self.handleData)
					self.$Utils.finishFunc('getHandleData');
				};
				self.$apis.productGet(postData, callback);
			},
			
			countPrice(){
				const self = this;
				var nowTime = Date.parse(new Date());
				self.totalPrice = 0;
				self.submitData.time_price=0;
				
				self.submitData.coupon_reduce=0;
				self.submitData.rider_income=0;
				self.submitData.platform_income=0;
				self.submitData.member_reduce=0;
				self.moneyMxDate = [];
				console.log(self.clickCur)
				self.totalPrice = parseFloat(self.handleData[self.clickCur].price);
				
				self.submitData.main_price = self.totalPrice;
				self.moneyMxDate.push({title:'起步价',price:'￥'+self.submitData.main_price});
				self.couponTotalPrice = self.$Utils.addItemInArray(self.pay.coupon, 'price');
				if(self.mainData.param1&&self.mainData.param1.length>0){
					var betweenTime = parseInt(self.time) - parseInt(self.handleData[self.clickCur].passage1);
					var ratio =Math.ceil(parseInt(betweenTime)/parseInt(self.mainData.param1[0].duration));
					console.log(ratio)
					self.submitData.time_price = ratio*self.mainData.param1[0].price;
					/* for (var i = 0; i < self.mainData.param1.length; i++) {
						if(i==0){
							self.submitData.time_price = (parseInt(self.time)-self.mainData.param1[i].duration)*self.mainData.param1[i].price
						}else{
							self.submitData.time_price = (self.mainData.param1[i-1].duration-self.mainData.param1[i].duration)*self.mainData.param1[i].price+ self.submitData.time_price
						}
						console.log('self.totalPrice',self.totalPrice)
					}; */
					self.submitData.time_price = parseFloat(self.submitData.time_price).toFixed(2);
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.time_price)).toFixed(2);
					self.moneyMxDate.push({title:'时长附加',range:parseInt(self.time)+'分钟',price:'￥'+self.submitData.time_price})
				};
				
				if(self.tip>0){
					self.submitData.gratuity = self.tip;
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.gratuity)).toFixed(2);
					self.moneyMxDate.push({title:'小费',price:'￥'+self.submitData.gratuity})
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
				console.log('wxPay',wxPay)
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
				
				console.log()
			},
			
			clickProTwo(index){
				const self = this;
				self.clickCurTwo = index
			},
			
			clickPro(index){
				const self = this;
				self.clickCur = index;
				self.countPrice()
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
			
			confirmTip(){
				const self = this;
				self.tippingShow();
				self.countPrice()
			},
			
			tipInput(event){
				const self = this;
				self.seltCurr = -1;
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
	.agentMsg .daibanNav ul{width: 100%; display: flex; flex-wrap:wrap;margin-top: 15px;}
	.agentMsg .daibanNav li{width: 20%; display: flex;flex-direction: column;font-size: 12px; text-align: center;}
	.agentMsg .daibanNav li .icon{width: 20px; height: 20px; margin: 0 auto 3px auto;-webkit-filter: grayscale(100%); -moz-filter: grayscale(100%);-ms-filter: grayscale(100%);-o-filter: grayscale(100%); filter: grayscale(100%); filter: gray(100%);
	}
	.agentMsg .daibanNav li.on .icon{-webkit-filter: grayscale(0); -moz-filter: grayscale(0);-ms-filter: grayscale(0);-o-filter: grayscale(0); filter: grayscale(0); filter: gray(0);}
</style>
