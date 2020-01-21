<template>
	<div>
		<div class="userInfor">
			<form action="" method="">
				<ul>
					<li>
						<span class="name">持卡人：</span>
						<input class="edit" type="text" v-model="submitData.name" placeholder="请输入持卡人的姓名">
					</li>
					<li>
						<span class="name">手机号：</span>
						<input class="edit" type="number" v-model="submitData.phone" placeholder="请输入您银行卡绑定的手机号" onkeyup="this.value=this.value.replace(/\D/g,'')"  maxlength="11">
					</li>
					<li>
						<span class="name">开户行：</span>
						<div class="rr edit" style="color: #666;">
							<picker @change="bindPickerChange" :value="index" :range="labelData" range-key="title">
								<div class="uni-input">{{labelData[index]&&labelData[index].title?labelData[index].title:'请选择'}}</div>
							</picker>
						</div>
					</li>
					<li>
						<span class="name">银行卡号：</span>
						<input class="edit" type="number" v-model="submitData.number" placeholder="请输入您的银行卡号">
					</li>
					<li>
						<div class="name" style="width: 50%;">设为默认银行卡</div>
						<div class="edit" style="width:50%;text-align: right">
							<switch @change="choose" :checked="submitData.isdefault==1?true:false" color="#2FA0ED"></switch>
							<!-- <img v-if="!is_show" class="mmIcon" src="../../static/images/address-icon03.png" alt="">
							<img v-if="is_show" class="mmIcon" src="../../static/images/address-icon02.png" alt=""> -->
						</div>
					</li>	
				</ul>
			</form>
		</div>
		
		<div class="submitbtn" style="margin-top: 100px;">
			<button class="btn" @click="Utils.stopMultiClick(submit)">确认</button>
		</div>
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
					number: '',
					phone: '',
					bank:'',
					isdefault:''
				},		
				index:'',
				labelData:[],
				Utils:this.$Utils
			}
		},
		onLoad(options) {
			const self = this;
			self.$Utils.loadAll(['getLabelData'], self)	
			if(options.id){
				self.id = options.id;
				self.getMainData()	
			}
		},
		
		methods: {
			
			bindPickerChange(e) {
				const self=this;
				console.log('picker发送选择改变，携带值为', e.target.value)
				self.index = e.target.value;
				self.submitData.bank=self.labelData[self.index].id
			},
			
			getLabelData() {
				const self = this;	
				const postData = {};	
				postData.searchItem = {
					thirdapp_id:2,	
				};
				postData.getBefore = {
					caseData: {
						tableName: 'Label',
						searchItem: {
							title: ['=', ['银行类型']],
						},
						middleKey: 'parentid',
						key: 'id',
						condition: 'in',
					},
				};
				postData.order = {
					create_time:'asc'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.labelData.push.apply(self.labelData, res.info.data);
					}
					console.log('self.labelData',self.labelData)
					self.$Utils.finishFunc('getLabelData');
				};
				self.$apis.labelGet(postData, callback);
			},
				
	
			
			choose(e) {
				const self = this;
				if(e.target.value){
					self.submitData.isdefault = 1
				}else{
					self.submitData.isdefault = 0
				}
				console.log('switch2 发生 change 事件，携带值为', e.target.value)
			},

			getMainData(id) {
				const self = this;
				const postData = {};
				postData.searchItem = {};
				postData.searchItem.id = self.id;
				postData.tokenFuncName = 'getProjectToken';

				const callback = (res) => {
					console.log(res);
					
					self.submitData.name = res.info.data[0].name;
					self.submitData.number = res.info.data[0].number;
					self.submitData.phone = res.info.data[0].phone;
					self.submitData.isdefault = res.info.data[0].isdefault;
					for (var i = 0; i < self.labelData.length; i++) {
						if(self.labelData[i].id==res.info.data[0].bank){
							self.index = i
						}
					}
				};
				self.$apis.cardGet(postData, callback);
			},

			cardUpdate() {
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
						setTimeout(function() {
							uni.navigateBack({
								delta:1
							})
						}, 1000);
						//self.$Router.redirectTo({route:{path:'/pages/user_myBankCard/user_myBankCard'}})
					} else {
						self.$Utils.showToast(data.msg,'error')
					};
					
				};
				self.$apis.cardUpdate(postData, callback);
			},


			cardAdd() {
				const self = this;
				const postData = {};

				postData.tokenFuncName = 'getProjectToken';

				postData.data = {};
				postData.data = self.$Utils.cloneForm(self.submitData);

				const callback = (data) => {
					uni.setStorageSync('canClick', true);
					if (data && data.solely_code == 100000) {
						self.$Utils.showToast('添加成功','success');
						setTimeout(function() {
							uni.navigateBack({
								delta:1
							})
						}, 1000);
					} else {
						self.$Utils.showToast(data.msg,'success')
					}
					
				};
				self.$apis.cardAdd(postData, callback);
			},


			submit() {
				const self = this;
				
				var phone = self.submitData.phone;
				const pass = self.$Utils.checkComplete(self.submitData);

				console.log('self.data.sForm', self.submitData)
				console.log('pass', pass)
				if (pass) {
					
					if (self.id) {

						self.cardUpdate();
					} else {

						self.cardAdd();
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
	@import "../../assets/style/userInfor.css";
	.myaddress-lis{padding:15px;margin-bottom: 10px;background: #fff; margin-bottom: 15px;border-radius: 5px; margin-top: 15px;}
	.myaddress-lis .name{  color: #222;padding:5px 0;}
	.myaddress-lis .adrs{ font-size: 13px;color: #999; line-height: 20px;}
	.seltBox{ display: flex;justify-content: space-between; align-items: center;padding-top: 5px;}
	.seltBox .L{ width: 30%; display: flex; align-items: center; font-size: 12px; color: #999;}
	.seltBox .L .icon{ width: 15px; height: 15px; display: inline-block; margin-right: 5px;}
	.seltBox .R{display: flex; align-items: center; width: 70%; justify-content: flex-end;}
	.seltBox .R .child{margin-left: 15px; display: flex; justify-content: flex-end; font-size: 12px; color: #999; align-items: center;}
	.seltBox .R img{  width:15px; height: 15px; display:block; margin-right: 4px;}
	
	.bankIcon{width:35px ; height: 35px; display: block; margin-right: 10px;}
	
	.btnB{ width: 60%;margin: 0 auto;}
	.btnB div{width: 50px; line-height: 25px; height: 25px;border-radius: 15px;border:1px solid #626262;}
	.btnB div.on{background: #2FA0ED;color: #fff;border-color: #2FA0ED;}
</style>	



