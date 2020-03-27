<template>
	<div>
		<div class="addressAdd bgwhite">
			<ul>
				<li>
					<div class="title">联系人姓名</div>
					<div class="rightMsg">
						<input type="text" placeholder="请输入真实姓名" v-model="submitData.name">
					 </div>
				</li>
				<li>
					<div class="title">手机号码</div>
					<div class="rightMsg">
						<input type="number" placeholder="请输入联系方式"  maxlength="11" v-model="submitData.phone">
					</div>
				</li>
				<!-- <li>
					<div class="title">所在地区</div>
					<div class="rightMsg" @click="showMulLinkageThreePicker">
						{{submitData.city==''?'请选择地区':submitData.city}}
						<img class="arrowR" src="../../static/images/icon.png" alt="">
					</div>
				</li> -->
				<li>
					<div class="title">选择位置</div>
					<div class="rightMsg" @click="chooseAddress()">
						<input type="text" placeholder="选择您的位置" disabled="true"   v-model="submitData.city">
					</div>
				</li>
				<li>
					<div class="title">详细地址</div>
					<div class="rightMsg">
						<input type="text" placeholder="如楼号、门牌号"   v-model="submitData.detail">
					</div>
				</li>
				<li >
					<div class="title">设为星标地址</div>
					<div class="rightMsg">
						<switch @change="choose" :checked="submitData.star==1?true:false" color="#2FA0ED"></switch>
					</div>
				</li>	
			</ul>
		</div>
		
		<div class="submitbtn" @click="Utils.stopMultiClick(submit)" style="margin-top: 130px;">
			<div class="btn">保存</div>
		</div>	
		<div class="submitbtn" @click="Router.redirectTo({route:{path:'/pages/address/address?name='+name}})">
			<div class="btn">查看已有地址</div>
		</div>	
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValueDefault"
		 @onCancel="onCancel" @onConfirm="onConfirm"></mpvue-city-picker>
	</div>

</template>

<script>
	import mpvuePicker from '../../components/mpvue-picker/mpvuePicker.vue';
	// https://github.com/zhetengbiji/mpvue-citypicker
	import mpvueCityPicker from '../../components/mpvue-citypicker/mpvueCityPicker.vue'
	import cityData from '../../common/city.data.js';

	export default {
		components: {
			mpvuePicker,
			mpvueCityPicker
		},
		
		
		data() {
			return {
				submitData: {
					name: '',
					city:'',
					detail: '',
					phone:'',
					longitude:'',
					latitude:'',
					star:''
				},
				
				mulLinkageTwoPicker: cityData,
				cityPickerValueDefault: [0, 0, 0],
				themeColor: '#F98A48',
				Utils:this.$Utils,
				Router:this.$Router,
				mode: '',
				deepLength: 1,
				pickerValueDefault: [0],
				pickerValueArray:[],
				
			}
		},
		onLoad(options) {
			const self = this;
			if(options.id){
				self.id = options.id;
				var res = self.$Token.getProjectToken(function(){
					self.$Utils.loadAll(['getMainData'], self)
				});
				if(res){
					self.$Utils.loadAll(['getMainData'], self)
				};
			}
			if(options.name){
				self.name = options.name
			}
		},
		
		methods: {
			
			choose(e) {
				const self = this;
				if(e.target.value){
					self.submitData.star = 1
				}else{
					self.submitData.star = 0
				}
				console.log('switch2 发生 change 事件，携带值为', e.target.value)
			},
			
			chooseAddress(e) {
				const self = this;
				uni.authorize({
				    scope: 'scope.userLocation',
				    success() {
				        uni.chooseLocation({
				        	success: (res) => {
				        		console.log(res)
				        		self.submitData.city = res.name;
								var newObject = self.$Utils.qqMapTransBMap(res.longitude,res.latitude)
							
				        		self.submitData.longitude = newObject.lng;
				        		self.submitData.latitude  = newObject.lat;
								console.log(self.submitData)
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
				        							self.submitData.city = res.name;
				        							var newObject = self.$Utils.qqMapTransBMap(res.longitude,res.latitude)
				        														
				        							self.submitData.longitude = newObject.lng;
				        							self.submitData.latitude  = newObject.lat;
				        							console.log(self.submitData)
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
			
			
			chooseLocation(){
				
				const self = this;
				uni.chooseLocation({
				    success: function (res) {
				        console.log('位置名称：' + res.name);
				        console.log('详细地址：' + res.address);
				        console.log('纬度：' + res.latitude);
				        console.log('经度：' + res.longitude);
						self.submitData.detail = res.address;
						self.submitData.longitude = res.longitude;
						self.submitData.latitude  = res.latitude;
				    },
					fail() {
						uni.authorize({
						    scope: 'scope.userLocation',
						    success() {
						      uni.chooseLocation({
						          success: function (res) {
						              console.log('位置名称：' + res.name);
						              console.log('详细地址：' + res.address);
						              console.log('纬度：' + res.latitude);
						              console.log('经度：' + res.longitude);
										self.submitData.detail = res.address;
										self.submitData.longitude = res.longitude;
										self.submitData.latitude  = res.latitude;
			
						          },
								})
						    }
						})
					}
				});
			},
			
			
			
			

			getMainData(id) {
				const self = this;
				const postData = {};
				postData.searchItem = {};
				postData.searchItem.id = self.id;
				postData.tokenFuncName = 'getProjectToken';

				const callback = (res) => {
					console.log(res);
					self.submitData.longitude = res.info.data[0].longitude;
					self.submitData.latitude  = res.info.data[0].latitude;
					self.submitData.name = res.info.data[0].name;
					self.submitData.detail = res.info.data[0].detail;
					self.submitData.star = res.info.data[0].star;
					self.submitData.phone = res.info.data[0].phone;
					self.submitData.city = res.info.data[0].city;
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.addressGet(postData, callback);
			},



			addressUpdate() {
				const self = this;
				const postData = {};

				postData.tokenFuncName = 'getProjectToken';

				postData.searchItem = {};
				postData.searchItem.id = self.id;
				postData.data = {};
				postData.data = self.$Utils.cloneForm(self.submitData);

				const callback = (data) => {
					uni.setStorageSync('canClick', true);
					if (data && data.solely_code == 100000) {
						self.$Utils.showToast('修改成功','success');
						self.Router.redirectTo({route:{path:'/pages/address/address?name='+self.name}})
						/* setTimeout(function() {
							uni.navigateBack({
								delta:1
							})
						}, 1000); */
						
					} else {
						self.$Utils.showToast(data.msg,'error')
					};
					
				};
				self.$apis.addressUpdate(postData, callback);
			},


			addressAdd() {
				const self = this;
				const postData = {};

				postData.tokenFuncName = 'getProjectToken';

				postData.data = {};
				postData.data = self.$Utils.cloneForm(self.submitData);

				const callback = (data) => {
					uni.setStorageSync('canClick', true);
					if (data && data.solely_code == 100000) {
						self.$Utils.showToast('添加成功','success');
						self.Router.redirectTo({route:{path:'/pages/address/address?name='+self.name}})
						/* setTimeout(function() {
							uni.navigateBack({
								delta:1
							})
						}, 1000); */
					} else {
						self.$Utils.showToast(data.msg,'success')
					}
					
				};
				self.$apis.addressAdd(postData, callback);
			},


			submit() {
				const self = this;
				
				var phone = self.submitData.phone;
				const pass = self.$Utils.checkComplete(self.submitData);

				console.log('self.data.sForm', self.submitData)
				console.log('pass', pass)
				if (pass) {
					
					if (self.id) {

						self.addressUpdate();
					} else {

						self.addressAdd();
					}

			
				} else {
					uni.setStorageSync('canClick', true);
					self.$Utils.showToast('请补全信息','success');
				};
			},

		}
	}
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/address.css";
</style>