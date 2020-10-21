<!DOCTYPE html>
<template>
	<div>
		<div>
			<!-- 标题 -->
			<!-- <div class="T-head line50 center fs16 Tfix-head">下单</div>
			<div class="h50"></div> -->

			<div class="flex T-head-msg">
				<a class="left" @click="Router.navigateTo({route:{path:'/pages/user/user'}})"><img src="../../static/images/home-icon1.png"></a>
				<div class="text">同城取送1小时送达</div>
				<div class="right" style="display: flex;align-items: center;justify-content:flex-end;">
					<!-- {{cityData.title?cityData.title:'未开通'}} -->

					<picker mode="selector" :range="allCityData" range-key="title" @change="changeCity">
						<view>{{allCityData[cityIndex]&&allCityData[cityIndex].title?allCityData[cityIndex].title:'未开通'}}</view>
					</picker>
					<div><img class="arrow" style="width: 20rpx;height:14rpx;margin-left: 10rpx;" src="../../static/images/home-icon2.png"
						 alt=""></div>
				</div>
			</div>

			<!-- banner -->
			<div class="ind_banner oh">
				<img :src="sliderData.mainImg&&sliderData.mainImg[0]?sliderData.mainImg[0].url:''" alt="">
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
							<!-- <input type="text" v-model="fromAddress.city&&fromAddress.detail?fromAddress.city+fromAddress.detail:fromAddress.city"
							 disabled="true" placeholder="从哪里发出"> -->
							<div style="width: 80%;" v-if="fromAddress.city" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=from'}})">
								{{fromAddress.city+fromAddress.detail}}
							</div>
							<div style="width: 80%;color: #888;" v-else @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=from'}})">
								从哪里发出
							</div>
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address/address?name=from'}})">地址薄</a>
						</li>
						<li>
							<i class="dian" style="background: #f01a1c;"></i>
							<!-- <input type="text" v-model="goAddress.city&&goAddress.detail?goAddress.city+goAddress.detail:goAddress.city"
							 disabled="true" placeholder="送到哪里去" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=go'}})"> -->
							 <div style="width: 80%;" v-if="goAddress.city" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=go'}})">
							 	{{goAddress.city+goAddress.detail}}
							 </div>
							 <div style="width: 80%;color: #888;" v-else @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=go'}})">
							 	送到哪里去
							 </div>
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address/address?name=go'}})">地址薄</a>
						</li>
						<li @click="Router.navigateTo({route:{path:'/pages/goodsInfor/goodsInfor?name=goodsInfo'}})">
							<div class="flexRowBetween" style="width: 100%;">
								<i class="dian" style="background: #f01a1c;"></i>
								<input type="text" disabled="true" value="" placeholder="请选择要配送的物品信息">
								<img class="arrow" src="../../static/images/icon.png" alt="">
							</div>
						</li>
					</ul>
				</div>
				<div class="agentMsg pdlr4" v-if="current==2">
					<div class="editTex" style="background: #f5f5f5;">
						<textarea style="width: 80%;" rows="" v-model="buyText" cols="4" placeholder="请输入你想买的商品"></textarea>
					</div>
					<a class="btn" style="z-index:999" @click="goBuy()">
						下单
					</a>
					<div class="proNav">
						<ul>
							<li :class="clickCur==index?'on':''" v-for="(item,index) in buyData" :key="index" @click="clickPro(index)">
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
					<a class="btn" style="z-index:999" @click="goHandle()">下单</a>
					<div class="daibanNav">
						<ul>
							<li :class="clickCurTwo==index?'on':''" v-for="(item,index) in handleData" :key="index" @click="clickProTwo(index)">
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
							<!-- <input type="text" v-model="sdFromAddress.city&&sdFromAddress.detail?sdFromAddress.city+sdFromAddress.detail:sdFromAddress.city"
							 disabled="true" placeholder="从哪里发出" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdFrom'}})"> -->
							 <div style="width: 80%;" v-if="sdFromAddress.city" 
							 @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdFrom'}})">
							 	{{sdFromAddress.city+sdFromAddress.detail}}
							 </div>
							 <div style="width: 80%;color: #888;" v-else @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdFrom'}})">
							 	从哪里发出
							 </div>
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address/address?name=sdFrom'}})">地址薄</a>
						</li>
						<li>
							<i class="dian" style="background: #f01a1c;"></i>
							<!-- <input type="text" v-model="sdGoAddress.city&&sdGoAddress.detail?sdGoAddress.city+sdGoAddress.detail:sdGoAddress.city"
							 disabled="true" placeholder="送到哪里去" @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdGo'}})"> -->
							 <div style="width: 80%;" v-if="sdGoAddress.city"
							 @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdGo'}})">
							 	{{sdGoAddress.city+sdGoAddress.detail}}
							 </div>
							 <div style="width: 80%;color: #888;" v-else @click="Router.navigateTo({route:{path:'/pages/address_add/address_add?name=sdGo'}})">
							 	送到哪里去
							 </div>
							<a class="bookBtn" @click="Router.navigateTo({route:{path:'/pages/address/address?name=sdGo'}})">地址薄</a>
						</li>
						<li @click="Router.navigateTo({route:{path:'/pages/goodsInfor/goodsInfor?name=sdGoodsInfo'}})">
							<div class="flexRowBetween" style="width: 100%;">
								<i class="dian" style="background: #f01a1c;"></i>
								<input type="text" disabled="true" value="" placeholder="请选择要配送的物品信息">
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
		<div v-show="showAuth" style="position: absolute;top: 0;left: 0;width: 100%;height: 100%;background-color: #000000;opacity: 0.5;">

		</div>
		<view v-show="showAuth" style="width: 80%;height: 300rpx;background-color: #fff;position: fixed;top: 30%;left: 10%;z-index: 999;border-radius: 20rpx;">
			<view class="font26 mgt30 center" style="font-weight: 700;">为了您更好的体验小程序，请授权登录~</view>
			<view style="display: flex;position: absolute;bottom: 0;width: 100%;">
				<button style="border-radius: 0;width: 50%;" @click="closeAuth">取消</button>
				<button open-type="getUserInfo" @getuserinfo="Utils.stopMultiClick(submit)" type="primary" style="border-radius: 0;width: 50%;">确定</button>
			</view>
		</view>
	</div>

</template>

<script>
	export default {
		data() {
			return {
				Router: this.$Router,
				is_show: false,
				mainData: [],
				clickCur: -1,
				buyData: [],
				handleData: [],
				buyText: '',
				handleText: '',
				clickCurTwo: -1,
				current: 1,
				persoList: [{}, {}, {}, {}],
				fromAddress: {},
				goAddress: {},
				sdFromAddress: {},
				sdGoAddress: {},
				goodsInfo: {},
				sdGoodsInfo: {},
				cityData: {},
				riderData: [],
				sliderData: {},
				allCityData: [],
				cityIndex: -1,
				showAuth: false,
				Utils: this.$Utils,
			}
		},

		onLoad(options) {
			const self = this;
			uni.removeStorageSync('fromAddress');
			uni.removeStorageSync('goAddress');
			uni.removeStorageSync('sdFromAddress');
			uni.removeStorageSync('sdGoAddress');
			uni.removeStorageSync('buyAddress');
			uni.removeStorageSync('handleAddress');
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			if (options.user_no) {
				const callback = (res) => {
					self.$Utils.loadAll(['getSliderData', 'getAllCityData', 'getUserData'], self);
				};
				self.$Token.getProjectToken(callback, {
					refreshToken: true,
					parent_no: options.user_no
				})
			} else if (options.scene) {
				var scene = decodeURIComponent(options.scene);
				const callback = (res) => {
					self.$Utils.loadAll(['getSliderData', 'getAllCityData', 'getUserData'], self);
				};
				self.$Token.getProjectToken(callback, {
					refreshToken: true,
					parent_no: scene
				})
			} else {
				self.$Utils.loadAll(['getSliderData', 'getAllCityData', 'getUserData'], self);
			}

		},

		onShow() {
			const self = this;
			if (uni.getStorageSync('fromAddress')) {
				self.fromAddress = uni.getStorageSync('fromAddress')
			} else {
				self.fromAddress = {}
			};
			if (uni.getStorageSync('goAddress')) {
				self.goAddress = uni.getStorageSync('goAddress')
			} else {
				self.goAddress = {}
			};
			if (uni.getStorageSync('sdFromAddress')) {
				self.sdFromAddress = uni.getStorageSync('sdFromAddress')
			} else {
				self.sdFromAddress = {}
			};
			if (uni.getStorageSync('sdGoAddress')) {
				self.sdGoAddress = uni.getStorageSync('sdGoAddress')
			} else {
				self.sdGoAddress = {}
			};
			if (uni.getStorageSync('goodsInfo')) {
				self.goodsInfo = uni.getStorageSync('goodsInfo')
			} else {
				self.goodsInfo = {}
			};
			if (uni.getStorageSync('sdGoodsInfo')) {
				self.sdGoodsInfo = uni.getStorageSync('sdGoodsInfo')
			} else {
				self.sdGoodsInfo = {}
			}
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

			tokenGet() {
				const self = this;
				const postData = {
					searchItem: {
						user_no: 'U716837043691036'
					}
				};
				console.log('postData', postData)
				const callback = (res) => {
					if (res.solely_code == 100000) {
						self.userData = res.info;
						uni.setStorageSync('user_token', res.token);
						uni.setStorageSync('user_info', res.info);
						var time = parseInt(new Date().getTime()) + 3500000;
						uni.setStorageSync('token_expire_time', time);
					}
					console.log('res', res)
					self.$Utils.finishFunc('tokenGet');
				};
				self.$apis.tokenGet(postData, callback);
			},

			closeAuth() {
				const self = this;
				self.showAuth = false;
			},

			submit() {
				const self = this;
				uni.setStorageSync('canClick', false);
				const callback = (user, res) => {
					console.log(res)
					self.getUserData();
				};
				self.$Utils.getAuthSetting(callback);
			},

			getUserData() {
				const self = this;
				const postData = {};
				console.log(222)
				postData.tokenFuncName = 'getProjectToken';
				if (!wx.getStorageSync('user_info') || wx.getStorageSync('user_info').headImgUrl == '' || !wx.getStorageSync(
						'user_info').headImgUrl) {
					postData.refreshToken = true;
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.userData = res.info.data[0];
						if (self.userData.headImgUrl == '') {
							self.showAuth = true;
						} else {
							self.showAuth = false;
						}
					}
					self.$Utils.finishFunc('getUserData');
				};
				self.$apis.userGet(postData, callback);
			},


			changeCity(e) {
				const self = this;
				console.log(e)
				self.cityIndex = e.detail.value;
				uni.setStorageSync('city_id', self.allCityData[e.detail.value].id);
				self.getBuyData();
				self.getHandleData();
			},

			getAllCityData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					type: 7
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.allCityData = res.info.data;
						self.getLocation()
					}
				};
				self.$apis.labelGet(postData, callback);
			},

			goBuy() {
				const self = this;
				if (self.clickCur < 0) {
					self.$Utils.showToast('未选择代买商品类型', 'none');
				} else {
					self.Router.navigateTo({
						route: {
							path: '/pages/replace_buy/replace_buy?index=' + self.clickCur + '&text=' + self.buyText
						}
					})
				}
			},

			goHandle() {
				const self = this;
				if (self.clickCurTwo < 0) {
					self.$Utils.showToast('未选择代办业务类型', 'none');
				} else {
					self.Router.navigateTo({
						route: {
							path: '/pages/replace_handle/replace_handle?index=' + self.clickCurTwo + '&text=' + self.handleText
						}
					})
				}
			},

			getSliderData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					title: '首页主图'
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

				} else {
					return {
						title: '极速达同城配送',
						path: '/pages/index/index?user_no=' + uni.getStorageSync('user_info').user_no, //点击分享的图片进到哪一个页面
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
					self.riderData = []
				};
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					user_type: 1
				};
				postData.order = {
					distance: 'asc',
					longitude: self.lng,
					latitude: self.lat,
				};
				postData.getBefore = {
					user: {
						tableName: 'User',
						middleKey: 'user_no',
						key: 'user_no',
						searchItem: {
							is_work: ['in', [1]]
						},
						condition: 'in'
					}
				};
				postData.tokenFuncName = 'getProjectToken';
				postData.paginate = {
					count: 0,
					currentPage: 1,
					is_page: true,
					pagesize: 5
				};
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
				var hasThisCity = false;
				const callback = (res) => {
					if (res) {
						console.log('res', res)
						if (res.authSetting) {
							self.$Utils.showToast('未授权位置信息，可在设置中开启', 'none')
							setTimeout(function() {
								self.getDefaultCity();
								self.$Utils.finishFunc('getAllCityData');
							}, 1000);
							return
						}
						self.city = res.address_component.city
						//self.city = '泰安市'
						self.lng = res.location.lng;
						self.lat = res.location.lat;
						self.getRiderData();
						for (var i = 0; i < self.allCityData.length; i++) {
							if (self.allCityData[i].title == self.city) {
								hasThisCity = true;
								self.cityIndex = i;
								uni.setStorageSync('city_id', self.allCityData[i].id);
							}
						}
						if (hasThisCity) {
							self.$Utils.finishFunc('getAllCityData');
							self.getBuyData();
							self.getHandleData();
						} else {
							self.getDefaultCity()
						}
					};
				};
				self.$Utils.getLocation('reverseGeocoder', callback);

			},



			getDefaultCity() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					isdefault: 1
				};
				const callback = (res) => {
					self.$Utils.finishFunc('getAllCityData');
					if (res.info.data.length > 0) {
						self.cityData = res.info.data[0];
						for (var i = 0; i < self.allCityData.length; i++) {
							if (self.allCityData[i].id == self.cityData.id) {
								self.cityIndex = i;
								uni.setStorageSync('city_id', self.allCityData[i].id);
							}
						}
						self.getBuyData();
						self.getHandleData();
					} else {
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
					city_id: uni.getStorageSync('city_id')
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
					listorder: 'desc'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.buyData = res.info.data;
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
					city_id: uni.getStorageSync('city_id')
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
					listorder: 'desc'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.handleData = res.info.data
					}
					console.log('self.handleData', self.handleData)
					self.$Utils.finishFunc('getHandleData');
				};
				self.$apis.productGet(postData, callback);
			},

			change(current) {
				const self = this;
				if (current != self.current) {
					self.current = current
				}
			},

			clickPro(index) {
				const self = this;
				self.clickCur = index
			},

			clickProTwo(index) {
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
