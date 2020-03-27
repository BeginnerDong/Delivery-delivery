<template>
	<div>
		<div class="">
			<a class="lineMsg flexRowBetween" @click="back()">
				<div>{{sdGoodsInfo.title}}、{{sdGoodsInfo.value}}、{{sdGoodsInfo.weight}}公斤</div>
				<img class="arrowR" src="../../static/images/icon.png" >
			</a>
		</div>
		<div class="f5H10"></div>
		
		<div class="pr">
			<div class="flexRowBetween qsCotMsg"  @click="Router.navigateTo({route:{path:'/pages/address/address?name=sdFrom'}})">
				<div class="left"><span class="note qu">取</span></div>
				<div class="flexRowBetween cont bordB1" v-if="sdFromAddress.city">
					<div class="infor">
						<h1 class="ftn fs14">{{sdFromAddress.city}}</h1>
						<p class="fs12 color9">{{sdFromAddress.detail}}</p>
					</div>
					<div class="phone">
						<p>{{sdFromAddress.phone}}</p>
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
			<div class="flexRowBetween qsCotMsg"  @click="Router.navigateTo({route:{path:'/pages/address/address?name=sdGo'}})">
				<div class="left"><span class="note shou">收</span></div>
				<div class="flexRowBetween cont" v-if="sdGoAddress.city">
					<div class="infor">
						<h1 class="ftn fs14">{{sdGoAddress.city}}</h1>
						<p class="fs12 color9">{{sdGoAddress.detail}}</p>
					</div>
					<div class="phone">
						<p>{{sdGoAddress.phone}}</p>
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
					<div class="left">愿意承担费用：</div>
					<div class="right">
						<input type="number" style="color: #000000;" placeholder-style="color:#999"  @blur="countPrice()" v-model="submitData.main_price"  :placeholder="'请输入支付金额(最低'+sdGoodsInfo.price+'元起)'">
						<img @click="Router.navigateTo({route:{path:'/pages/order_sameDay_notes/order_sameDay_notes?type=price'}})" src="../../static/images/dangrida-icon.png" style="width:18px ; height: 18px; display: block; margin-left:5px;">
					</div>
				</li>
				<li class="flexRowBetween">
					<div class="left">备注：</div>
					<div class="right">
						<input type="text" style="color: #000000;" v-model="submitData.passage1" placeholder-style="color:#999"  value="" placeholder="物品描述或送件要求" />
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
					<div class="left">是否同意协商价格：</div>
					<div class="right" @click="agreenMny">
						<img v-if="submitData.is_negotiate==0" src="../../static/images/dangrida-icon1.png" alt="" style="width:38px; height:20px;">
						<img v-if="submitData.is_negotiate==1" src="../../static/images/dangrida-icon2.png" alt="" style="width:38px; height:20px;">
					</div>
				</li>
			</ul>
		</div>
		<div><a class="red fs12 mglr4 line40"  
		@click="Router.navigateTo({route:{path:'/pages/order_sameDay_notes/order_sameDay_notes?type=order'}})">《下单须知》</a></div>
		
		<div class="qusUnderFix flexRowBetween">
			<div class="left flex fs12" @click="moneyMxShow">
				总价&nbsp;<em class="price ftw">{{pay.wxPay?pay.wxPay.price:'0.00'}}</em>
				<img class="arrowR" src="../../static/images/icon.png" >
			</div>
			<div class="right" @click="Utils.stopMultiClick(submit)">提交订单</div>
		</div>
		
		<!-- 弹框背景 -->
		<div class="black-bj" v-show="is_show"></div>
		
			
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
					<a class="pucolor" href="baojia_treaty.html" >《保价协议》</a>
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
				<div class="right" @click="Utils.stopMultiClick(submit)">提交订单</div>
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
				mainData: [],
				is_show:false,
				isAgree:false,
				is_baojiaShow:false,
				is_tippingShow:false,
				is_moneyMxShow:false,
				seltCurr:-1,
				seltCur:0,
				seltData:0,
				tipDate:[{name:'不加了',value:0},{name:'2元',value:2},{name:'5元',value:5},
				{name:'10元',value:10},{name:'15元',value:15},{name:'20元',value:20}],
				moneyMxDate:[
					
				],
				submitData:{
					gratuity:0,
					passage1:'',//备注
					start_time:'',//取件时间,
					main_price:'',
					is_negotiate:0,
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
					transport_status:1,
					total_distance:''
				},
		
				sdFromAddress:{},
				sdGoAddress:{},
				sdGoodsInfo:{},
				totalPrice:0,
				bjFee:0,
				tip:'',
				pay:{
					coupon:[]
				},
				
			}
		},

		onLoad() {
			const self = this;
			self.submitData.city_id= uni.getStorageSync('city_id')
		},
		
		onShow() {
			const self = this;
			var now = Date.parse(new Date())/1000;
			self.submitData.invalid_time = now+3600;
			if(uni.getStorageSync('sdFromAddress')){
				
				self.sdFromAddress = uni.getStorageSync('sdFromAddress');
				self.submitData.start_site = self.sdFromAddress.city+self.sdFromAddress.detail;
				self.submitData.start_latitude = self.sdFromAddress.latitude;
				self.submitData.start_longitude = self.sdFromAddress.longitude;
				self.submitData.start_name = self.sdFromAddress.name;
				self.submitData.start_phone = self.sdFromAddress.phone;
			};
			if(uni.getStorageSync('sdGoAddress')){
				self.sdGoAddress = uni.getStorageSync('sdGoAddress');
				self.submitData.end_site = self.sdGoAddress.city+self.sdGoAddress.detail;
				self.submitData.end_latitude = self.sdGoAddress.latitude;
				self.submitData.end_longitude = self.sdGoAddress.longitude;
				self.submitData.end_name = self.sdGoAddress.name;
				self.submitData.end_phone = self.sdGoAddress.phone;
			};
			if(uni.getStorageSync('sdGoodsInfo')){
				
				
				self.sdGoodsInfo = uni.getStorageSync('sdGoodsInfo');
				self.submitData.weight = self.sdGoodsInfo.weight;
				self.submitData.value = self.sdGoodsInfo.value;
			};
			
			if(uni.getStorageSync('sdFromAddress')&&uni.getStorageSync('sdGoAddress')){
				self.getBaiduDistance()
			}
		},

		methods: {
			
			back(){
				const self = this;
				uni.navigateBack({
					delta:1
				})
			},
			
			getBaiduDistance() {
				const self = this;
				const postData = {
					tokenFuncName:'getProjectToken',
					start_longitude:parseFloat(self.sdFromAddress.longitude),
					start_latitude:parseFloat(self.sdFromAddress.latitude),
					end_longitude:parseFloat(self.sdGoAddress.longitude),
					end_latitude:parseFloat(self.sdGoAddress.latitude)
				};
				const callback = (res) => {
					if(res.solely_code==100000){
						//self.distance = parseFloat(res.info.distance/1000).toFixed(2);
						self.submitData.total_distance = self.distance;
					}
				};
				self.$apis.getBaiduDistance(postData, callback);
			},
			
			countPrice(){
				const self = this;
				var nowTime = Date.parse(new Date());
				self.totalPrice = 0;
				self.submitData.gratuity=0;
				self.submitData.insured_price=0;
				self.submitData.rider_income=0;
				self.submitData.platform_income=0;
				self.moneyMxDate = [];
				if(parseFloat(self.submitData.main_price)<parseFloat(self.sdGoodsInfo.price)){
					self.submitData.main_price = '';
					self.$Utils.showToast('支付价格必须大于或等于指导价格', 'none');
					return;
				};
				if(parseFloat(self.submitData.main_price)>0){
					self.totalPrice = (parseFloat(self.totalPrice) +  parseFloat(self.submitData.main_price)).toFixed(2);
					self.moneyMxDate.push({title:'愿意承担费用',price:'￥'+self.submitData.main_price})
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
			
				
				self.submitData.platform_income = (parseFloat(self.totalPrice)*(parseFloat(uni.getStorageSync('user_info').thirdApp.custom_rule.tax)/100)).toFixed(2);
				
				var wxPay = parseFloat(self.totalPrice)
				if (wxPay > 0) {
					self.pay.wxPay = {
						price: wxPay.toFixed(2),
					};
				} else {
					  delete self.pay.wxPay;	 
				};
				self.submitData.rider_income = (wxPay.toFixed(2) - parseFloat(self.submitData.platform_income)).toFixed(2);
				self.submitData.price = wxPay.toFixed(2);
			},
			
			submit(){
				const self = this;
				uni.setStorageSync('canClick',false);
				
				if(self.submitData.main_price==''||parseFloat(self.submitData.main_price)<=0){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请输入正确的费用', 'none');
					return;
				};
				
				if(JSON.stringify(self.sdFromAddress)=="{}"){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请选择起始地点', 'none');
					return;
				};
				
				if(JSON.stringify(self.sdGoAddress)=="{}"){
					uni.setStorageSync('canClick',true);
					self.$Utils.showToast('请选择终点地点', 'none');
					return;
				};
				var data = self.$Utils.cloneForm(self.submitData);
				console.log('self.sdGoodsInfo',self.sdGoodsInfo)
				var orderList = [
					{product_id:self.sdGoodsInfo.id,count:1,type:4,data:data}
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
						uni.removeStorageSync('sdFromAddress');
						uni.removeStorageSync('sdGoAddress');
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
				postData.tokenFuncName = 'getProjectToken';
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
			
			tipInput(event){
				const self = this;
				self.seltCurr = -1;
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
			tippingShow(){
				const self = this;
				
				self.is_show = !self.is_show
				self.is_tippingShow = !self.is_tippingShow
				if(self.seltCurr==-1){
					self.seltCurr=0;
					self.tip = self.tipDate[0].value;
				}
				
			},
			
			seltSpecs(index){
				const self = this;
				self.seltCurr=index;
				self.tip =self.tipDate[self.seltCurr].value;
				console.log('self.tip',self.tip)
			},
			
			agreenMny(){
				const self = this;
				if(self.submitData.is_negotiate==0){
					self.submitData.is_negotiate=1
				}else if(self.submitData.is_negotiate==1){
					self.submitData.is_negotiate=0
				}
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