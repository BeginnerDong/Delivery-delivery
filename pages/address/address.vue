
<template>
	<div>
		<div class="addressLis">
			<ul style="padding-bottom: 200rpx;">
				<li v-for="(item,index) in mainData" :key="index">
					<h1 class="name flexRowBetween">
						<div class="flexRowBetween"  @click="choose(index)">
							{{item.name}}
							<em class="phone" style="margin-left: 10rpx;">{{item.phone}}</em>
						</div>
						
						<div class="left">
							<img class="xing" :src="item.star==1?'../../static/images/address-icon2.png':''">
						</div>
						
						<div class="right">
							<div class="tt" :data-id="item.id" 
							@click="Router.navigateTo({route:{path:'/pages/address_add/address_add?id='+$event.currentTarget.dataset.id+'&name='+name}})">
								<img src="../../static/images/address-icon.png" alt="">
								<span>编辑</span>
							</div>
							<div class="tt" :data-id="item.id" @click="deleteAddress($event.currentTarget.dataset.id)">
								<img src="../../static/images/address-icon1.png" alt="">
								<span>删除</span>
							</div>
						</div>
					</h1>
					<p class="adrs"  @click="choose(index)">{{item.city+item.detail}}</p>
					<!-- <div class="flexRowBetween">
						
						
					</div> -->
				</li>
			</ul>
		</div>
		
		<div class="submitbtn" style="margin-top: 80px;position: fixed;">
			<div class="btn" style="position: fixed;width:100%;bottom: 0;margin: 0;border-radius: 0;"  
			 @click="Router.redirectTo({route:{path:'/pages/address_add/address_add?name='+name}})">添加地址</div>
		</div>
	</div>

</template>

<script> 
	export default {
		data() {
			return {
				mainData: [],
				Router:this.$Router,
				choosedIndex: -1,
				addressLis:[
					{xingUrl:'../../static/images/address-icon2.png'},
					{xingUrl:'../../static/images/address-icon2.png'},
					{xingUrl:''}
				]
			}
		},

		onLoad(options) {
			const self = this;
			self.name = options.name; 
			if(options.from){
				self.no = true
			};
			
		},
		
		onReachBottom() {
			console.log('onReachBottom')
			const self = this;
			if (!self.isLoadAll && uni.getStorageSync('loadAllArray')) {
				self.paginate.currentPage++;
				self.getMainData()
			};
		},
		
		onShow() {
			const self = this;
			self.mainData = [];
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			var res = self.$Token.getProjectToken(function() {
				self.$Utils.loadAll(['getMainData'], self)
			});
			if (res) {
				self.$Utils.loadAll(['getMainData'], self)
			};
		},

		methods: {
			
			choose(index) {
				const self = this;
				if(self.no){
					return
				};
				self.choosedIndex = index;
				uni.setStorageSync(self.name+'Address', self.mainData[index]);
				console.log('choosedIndex', self.choosedIndex);
				uni.navigateBack({
					delta:1
				})
			},
			
			getMainData() {
				const self = this;
				const postData = {};
				postData.paginate = self.$Utils.cloneForm(self.paginate);
				postData.tokenFuncName = 'getProjectToken';
				
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData.push.apply(self.mainData, res.info.data);
					} else {
						self.$Utils.showToast('没有更多了', 'none');
					};
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.addressGet(postData, callback);
			},
			
			
			
			
			
			deleteAddress(id) {
				const self = this;
				const postData = {};
				postData.searchItem = {};
				postData.searchItem.id = id;
				postData.tokenFuncName = 'getProjectToken';
				const callback = (res) => {
					if (res) {
						self.mainData = [];
						self.getMainData();
					}
				};
				self.$apis.addressDelete(postData, callback)
			},
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/address.css";
</style>
