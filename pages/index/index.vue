<!DOCTYPE html>
<template>
	<div>
		<div>
			<!-- 标题 -->
			<!-- <div class="T-head line50 center fs16 Tfix-head">下单</div>
			<div class="h50"></div> -->
			
			<div class="flex T-head-msg">
				<a class="left" @click="Router.navigateTo({route:{path:'/pages/user/user'}})"><img  src="../../static/images/home-icon1.png" ></a>
				<div class="text">同城取送1小时送达</div>
				<div class="right" style="display: flex;align-items: center;justify-content: center;">
				{{cityData.title?cityData.title:'未开通'}}</div>
			</div>
			
			<!-- banner -->
			<div class="ind_banner oh">
				<img  :src="sliderData.mainImg&&sliderData.mainImg[0]?sliderData.mainImg[0].url:''" alt="">
			</div>
			<!-- banner end -->
			
			<div class="orderNav">
				<span class="tt" :class="current==1?'on':''" @click="change('1')">取送件</span>
				<span class="tt" :class="current==2?'on':''" @click="change('2')">代买</span>
				<span class="tt" :class="current==3?'on':''" @click="change('3')">代办</span>
				<span class="tt" :class="current==4?'on':''" @click="change('4')">当日达</span>
			</div>
			
			<div class="">
				<div class="takeMsg" v-if="current==1">
					<ul>
						<li>
							<i class="dian" style="background: #009944;"></i>
							<input type="text" v-model="fromAddress.city&&fromAddress.detail?fromAddress.city+fromAddress.detail:fromAddress.city" disabled="true" placeholder="从哪里发出">
							<a class="bookBtn" 
							@click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=from'}})">地址薄</a>
						</li>
						<li>
							<i class="dian" style="background: #f01a1c;"></i>
							<input type="text" v-model="goAddress.city&&goAddress.detail?goAddress.city+goAddress.detail:goAddress.city" disabled="true" placeholder="送到哪里去">
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=go'}})">地址薄</a>
						</li>
						<li @click="Router.navigateTo({route:{path:'/pages/goodsInfor/goodsInfor?name=goodsInfo'}})">
							<div class="flexRowBetween"  style="width: 100%;">
								<i class="dian" style="background: #f01a1c;"></i>
								<input type="text" value="" placeholder="请选择要配送的物品信息">
								<img class="arrow" src="../../static/images/icon.png" alt="">
							</div>
						</li>
					</ul>
				</div>
				<div class="agentMsg pdlr4" v-if="current==2">
					<div class="editTex" style="background: #f5f5f5;">
						<textarea style="width: 80%;" rows="" v-model="buyText" cols="4" placeholder="请输入你想买的商品"></textarea>
					</div>
					<a class="btn" style="z-index:999" 
					@click="Router.navigateTo({route:{path:'/pages/replace_buy/replace_buy?index='+clickCur+'&text='+buyText}})">
						下单
					</a>
					<div class="proNav">
						<ul>
							<li :class="clickCur==index?'on':''" v-for="(item,index) in buyData"  
							:key="index" @click="clickPro(index)">
								<img class="icon" :src="item.mainImg&&item.mainImg[0]?item.mainImg[0].url:''">
								<span>{{item.title}}</span>
							</li>
						</ul>
					</div>
				</div>
				<div class="agentMsg pdlr4" v-if="current==3">
					<div class="editTex" style="background: #f5f5f5;">
						<textarea style="width: 80%;" rows="" v-model="handleText" cols="4" placeholder="需求详细说明"></textarea>
					</div>
					<a class="btn" style="z-index:999" 
					@click="Router.navigateTo({route:{path:'/pages/replace_handle/replace_handle?index='+clickCurTwo+'&text='+handleText}})">下单</a>
					<div class="daibanNav">
						<ul>
							<li :class="clickCurTwo==index?'on':''" v-for="(item,index) in handleData"  :key="index" @click="clickProTwo(index)">
								<img class="icon" :src="item.mainImg&&item.mainImg[0]?item.mainImg[0].url:''">
								<span>{{item.title}}</span>
							</li>
						</ul>
					</div>
				</div>
				
				<div class="takeMsg" v-if="current==4">
					<ul>
						<li>
							<i class="dian" style="background: #009944;"></i>
							<input type="text" v-model="sdFromAddress.city&&sdFromAddress.detail?sdFromAddress.city+sdFromAddress.detail:sdFromAddress.city" disabled="true" placeholder="从哪里发出">
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdFrom'}})">地址薄</a>
						</li>
						<li>
							<i class="dian" style="background: #f01a1c;"></i>
							<input type="text" v-model="sdGoAddress.city&&sdGoAddress.detail?sdGoAddress.city+sdGoAddress.detail:sdGoAddress.city" disabled="true" placeholder="送到哪里去">
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdGo'}})">地址薄</a>
						</li>
						<li @click="Router.navigateTo({route:{path:'/pages/goodsInfor/goodsInfor?name=sdGoodsInfo'}})">
							<div class="flexRowBetween"  style="width: 100%;">
								<i class="dian" style="background: #f01a1c;"></i>
								<input type="text" value="" placeholder="请选择要配送的物品信息">
								<img class="arrow" src="../../static/images/icon.png" alt="">
							</div>
						</li>
					</ul>
				</div>
			</div>
			
			<div class="bordB1"></div>
			<div class="persoList pdlr4">
				<ul>
					<li v-for="(item,index) in riderData" :key="index">
						<div class="left">
							<img :src="item.mainImg&&item.mainImg[0]?item.mainImg[0].url:''" alt="">
							<span>{{item.name}}</span>
						</div>
						<div class="right">距离您的位置<em class="mm">{{item.distance}}km</em></div>
					</li>
				</ul>
			</div>
		
		</div>
	</div>

</template>

<script>
	export default {
		data() {
			return {
				Router:this.$Router,
				is_show:false,
				mainData: [],
				clickCur:0,
				buyData:[],
				handleData:[],
				buyText:'',
				handleText:'',
				clickCurTwo:0,
				current:1,
				persoList:[{},{},{},{}],
				fromAddress:{},
				goAddress:{},
				sdFromAddress:{},
				sdGoAddress:{},
				goodsInfo:{},
				sdGoodsInfo:{},
				cityData:{},
				riderData:[],
				sliderData:{}
			}
		},

		onLoad(options) {
			const self = this;
			uni.removeStorageSync('fromAddress');
			uni.removeStorageSync('goAddress');
			uni.removeStorageSync('sdFromAddress');
			uni.removeStorageSync('sdGoAddress');
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			if(options.user_no){
				const callback = (res) => {
					self.$Utils.loadAll(['getLocation','getSliderData'], self);
				};
				self.$Token.getProjectToken(callback, {
					refreshToken: true,parent_no:options.user_no
				})
			}else{
				const callback = (res) => {
					self.$Utils.loadAll(['getLocation','getSliderData'], self);
				};
				self.$Token.getProjectToken(callback, {
					refreshToken: true
				})
			}
			
		},
		
		onShow() {
			const self = this;
			if(uni.getStorageSync('fromAddress')){
				self.fromAddress = uni.getStorageSync('fromAddress')
			};
			if(uni.getStorageSync('goAddress')){
				self.goAddress = uni.getStorageSync('goAddress')
			};
			if(uni.getStorageSync('sdFromAddress')){
				self.sdFromAddress = uni.getStorageSync('sdFromAddress')
			};
			if(uni.getStorageSync('sdGoAddress')){
				self.sdGoAddress = uni.getStorageSync('sdGoAddress')
			};
			if(uni.getStorageSync('goodsInfo')){
				self.goodsInfo = uni.getStorageSync('goodsInfo')
			};
			if(uni.getStorageSync('sdGoodsInfo')){
				self.sdGoodsInfo = uni.getStorageSync('sdGoodsInfo')
			};
		},


		onReachBottom() {
			console.log('onReachBottom')
			const self = this;
			if (!self.isLoadAll && uni.getStorageSync('loadAllArray')) {
				self.paginate.currentPage++;
				self.getRiderData()
			};
		},

		onPullDownRefresh() {
			console.log('refresh');
			const self = this;
			self.getRiderData(true)
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
		},

		

		methods: {
			
			getSliderData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					title:'首页主图'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.sliderData = res.info.data[0]
					}
					console.log('self.sliderData', self.sliderData)
					self.$Utils.finishFunc('getSliderData');
				};
				self.$apis.labelGet(postData, callback);
			},
			
			onShareAppMessage: function(ops) {
				console.log(ops)
				const self = this;
				if (ops.from === 'button') {
					
				}else{
					return {
						title: '极速达同城配送',
						path: '/pages/index/index?user_no='+uni.getStorageSync('user_info').user_no, //点击分享的图片进到哪一个页面
						success: function(res) {
							// 转发成功
							console.log("转发成功:" + JSON.stringify(res));
						},
						fail: function(res) {
							// 转发失败
							console.log("转发失败:" + JSON.stringify(res));
						}
					}
					console.log(ops.target)
				}
				
			},
			
			getRiderData(isNew) {
				const self = this;
				if (isNew) {
					self.riderData = [];
					self.paginate = {
						count: 0,
						currentPage: 1,
						is_page: true,
						pagesize: 5
					}
				};
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					user_type:1
				};
				postData.order = {
					distance:'asc',
					longitude:self.lng,
					latitude:self.lat,
				};
				postData.tokenFuncName = 'getProjectToken';
				postData.paginate = self.$Utils.cloneForm(self.paginate);
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.riderData.push.apply(self.riderData, res.info.data);
						for (var i = 0; i < self.riderData.length; i++) {
							self.riderData[i].distance =
								self.$Utils.distance(self.lat, self.lng, self.riderData[i].latitude, self.riderData[i].longitude)
							
						}
					}
					console.log('self.riderData', self.riderData)
				};
				self.$apis.userInfoGet(postData, callback);
			},
			
			getLocation() {
				const self = this;
				const callback = (res) => {
					if (res) {
						console.log('res', res)
						if (res.authSetting) {
							console.log(232)
							return
						}
						self.city = res.address_component.city
						//self.city = '泰安市'
						self.lng = res.location.lng;
						self.lat = res.location.lat
						self.getCityData();
						self.getRiderData(true)
					};
				};
				self.$Utils.getLocation('reverseGeocoder', callback);
			
			},
			
			getCityData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					title: self.city
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.cityData = res.info.data[0];
						uni.setStorageSync('city_id', self.cityData.id);
						self.getBuyData();
						self.getHandleData();
						self.$Utils.finishFunc('getLocation');
					} else {
						self.getDefaultCity()
					}
			
				};
				self.$apis.labelGet(postData, callback);
			},
			
			getDefaultCity() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					is_default: 1
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.cityData = res.info.data[0];
						uni.setStorageSync('city_id', self.cityData.id);
						self.getBuyData();
						self.getHandleData();
						self.$Utils.finishFunc('getLocation');
					} else {
						self.getDefaultCity()
						uni.showModal({
							title: '提示',
							content: '当前城市未开通',
							success: function(res) {
								if (res.confirm) {
									 
								} else if (res.cancel) {
									console.log('用户点击取消');
								}
							}
						});
					}
			
				};
				self.$apis.labelGet(postData, callback);
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
					}
					console.log('self.handleData', self.handleData)
					self.$Utils.finishFunc('getHandleData');
				};
				self.$apis.productGet(postData, callback);
			},
			
			change(current){
				const self = this;
				if(current!=self.current){
					self.current = current
				}
			},
			
			clickPro(index){
				const self = this;
				self.clickCur = index
			},
			
			clickProTwo(index){
				const self = this;
				self.clickCurTwo = index
			},
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/orderNav.css";
	@import "../../assets/style/index.css";
	
</style>
