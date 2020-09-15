<template>
	<div style="padding-bottom: 50px;">
		<div class="userHead">
			<div class="left">
				<div class="photo" style="overflow: hidden;">
					<open-data type="userAvatarUrl"></open-data>
				</div>
				<div style="width: 70%;"><open-data type="userNickName"></open-data></div>
			</div>
		</div>
		
		<div class="userBox2 boxShaow radius10">
			<div class="flexRowBetween tit">
				<div>我的订单</div>
				<a class="more flex"  
				@click="Router.navigateTo({route:{path:'/pages/user_myOrder/user_myOrder'}})">查看<img class="arrowR" src="../../static/images/icon.png" ></a>
			</div>
			<div class="menu flexRowBetween">
				<div class="child" 
				@click="Router.navigateTo({route:{path:'/pages/user_myOrder/user_myOrder?num=1'}})">
					<a href="#">
						<img src="../../static/images/about-icon.png"/>
						<p>全部订单</p>
					</a>
					
				</div>
				<div class="child"
				@click="Router.navigateTo({route:{path:'/pages/user_myOrder/user_myOrder?num=2'}})">
					<a href="#">
						<img src="../../static/images/about-icon1.png">
						<p>待接单</p>
					</a>
				</div>
				<div class="child" 
				@click="Router.navigateTo({route:{path:'/pages/user_myOrder/user_myOrder?num=3'}})">
					<a href="#">
						<img src="../../static/images/about-icon2.png">
						<p>进行中</p>
					</a>
				</div>
				<div class="child"
				@click="Router.navigateTo({route:{path:'/pages/user_myOrder/user_myOrder?num=4'}})">
					<a href="#">
						<img src="../../static/images/about-icon3.png">
						<p>已完成</p>
					</a>
				</div>
			</div>
		</div>
		
		<div class="XlineNav boxShaow radius10 mglr4" style="margin-top: 20px;">
			<!-- <div class="info">
				<a href="user_infor.html">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon4.png">
						<span class="tt">基本信息</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div> -->
			<div class="info">
				<a  @click="Router.navigateTo({route:{path:'/pages/user_coupon/user_coupon'}})">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon5.png">
						<span class="tt">优惠券</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div>
			<div class="info">
				<a  @click="Router.navigateTo({route:{path:'/pages/user_VIPservice/user_VIPservice'}})">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon6.png">
						<span class="tt">会员卡</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div>
			<div class="info">
				<a  @click="Router.navigateTo({route:{path:'/pages/user_myMoney/user_myMoney'}})">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon7.png">
						<span class="tt">我的收益</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div>
			<div class="info">
				<a  @click="Router.navigateTo({route:{path:'/pages/address/address?from=user'}})">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon8.png">
						<span class="tt">地址薄</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div>
			<div class="info">
				<a  @click="Router.navigateTo({route:{path:'/pages/user_treaty/user_treaty'}})">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon9.png">
						<span class="tt">服务协议</span>
					</div>
					<div class="right">
						<img class="arrowR" src="../../static/images/icon.png" >
					</div>
				</a>
			</div>
			<div class="info">
				<a @click="phoneCall()">
					<div class="flex">
						<img class="icon" src="../../static/images/about-icon10.png">
						<span class="tt">联系客服</span>
					</div>
					<div class="right">
						<em class="color9 mgr10 fs12">{{mainData.description}}</em>
						<img src="../../static/images/about-icon11.png" style="width: 16px; height: 16px; display: block;" />
					</div>
				</a>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				Router: this.$Router,


				mainData: {}
			}
		},

		onLoad(options) {
			const self = this;
			self.$Utils.loadAll(['getMainData'], self);
		},

		methods: {
			
			phoneCall() {
				const self = this;
				wx.makePhoneCall({
					phoneNumber: self.mainData.description,
				})
			},

			getMainData() {
				const self = this;
				console.log('852369')
				const postData = {};
				postData.searchItem = {
					thirdapp_id:2,
					title:'客服电话'
				};
				const callback = (res) => {
					if (res.solely_code == 100000 && res.info.data[0]) {
						self.mainData = res.info.data[0];
					} else {
						self.$Utils.showToast(res.msg, 'none')
					};
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.labelGet(postData, callback);
			},
		}
	}
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/user.css";
</style>	
