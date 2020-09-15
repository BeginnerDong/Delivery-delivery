<template>
	<div>
		<!-- 标题 -->

		<div class="pdlr4">
			<div class="wpMsgBox">
				<div class="title">物品类型</div>
				<div class="item">
					<span v-for="(item,index) in mainData" :key="index" 
					:class="[seltCurr == index?'on':'']" @click="seltSpecs(index)">{{item.title}}</span>
				</div>
			
				
				
				<div class="title">物品价值</div>
				<div class="item">
					<span v-for="(item,index) in moneyDate" :key="index" :class="[seltCurrTwo == index?'on':'']" @click="seltSpecsTwo(index)">{{item}}</span>
				</div>
				<div class="" style="color: #9999;font-size: 12px;">温馨提示：请勿配送违禁品及3000元以上物品</div>
				<div class="title">物品重量</div>
				<div class="center fs18 pucolor">{{rangeValues[1]==0?'5公斤以下':parseFloat(rangeValues[1])+5+'公斤'}}</div>
				<div class="weightRange">
					<RangeSlider
						:width="slideWidth"
						:height="slideHeight"
						:blockSize="slideBlockSize"
						:min="slideMin"
						:max="slideMax"
						:values="rangeValues"
						:step="step"
						:liveMode="isLiveMode"
						@rangechange="onRangeChange"
					>
						<view slot="minBlock" class="range-slider-block"></view>
						<!-- 左边滑块的内容 -->
						<view slot="maxBlock" class="range-slider-block"></view>
						<!-- 右边滑块的内容 -->
					</RangeSlider>
					<!-- <span class="item">
						<em class="yuan"></em>
						<p>小于5公斤</p>
					</span> -->
				</div>
				
			</div>
		</div>
		
		<div class="submitbtn"   @click="submit()" style="margin-top: 100px;" v-if="name=='goodsInfo'">
			<div class="btn" >确定(取送件)</div>
		</div>
		<div class="submitbtn"   @click="submit()" style="margin-top: 100px;" v-if="name=='sdGoodsInfo'">
			<div class="btn" >确定(当日达)</div>
		</div>
	</div>
</template>

<script>
	import RangeSlider from '../../components/range-slider/range-slider.vue';
	export default {
		data() {
			return {
				minValue: 0,
				maxValue: 24,
				rangeValues: [0, 0],
				slideWidth: 500,
				slideHeight: 80,
				slideBlockSize: 56,
				slideMin: 0,
				slideMax: 20,
				//
				rangeValues2: [0, 10],
				serTime: '00:00:00-10:00:00',
				time: '13.5小时',
				step:0.1,
				isLiveMode:true,
				Router:this.$Router,
				is_show:false,
				info:{},
				mainData:[],
				moneyDate:['50元以下','50-150元','150-300元','300-1000元','1000-1500元','1500以上'],
				seltCurr:0,
				seltCurrTwo:0,
				name:'',
				getBefore:{
					caseData: {
						tableName: 'Label',
						searchItem: {
							title: ['=', ['取件']],
						},
						middleKey: 'category_id',
						key: 'id',
						condition: 'in',
					},
				},
				type:1
			}
		},
		components: {
			RangeSlider
		},
		onLoad(options) {
			const self = this;
			self.name = options.name;
			console.log(self.name)
			if(self.name=='sdGoodsInfo'){
				self.getBefore = {
					caseData: {
						tableName: 'Label',
						searchItem: {
							title: ['=', ['当日达']],
						},
						middleKey: 'category_id',
						key: 'id',
						condition: 'in',
					},
				};
				self.type=4
			};
			self.$Utils.loadAll(['getMainData'], self);
			/* const callback = (res) => {
				self.$Utils.loadAll(['getSliderData', 'getLabelData', 'getLocation', 'getLabelTwoData'], self);
			};
			self.$Token.getProjectToken(callback, {
				refreshToken: true
			}) */
		},
		
		onShow() {
			const self = this;
			console.log(self.info)
		},

		methods: {
			
			
			
			onRangeChange: function(e) {
				const self = this;
				this.rangeValues = [e.minValue, e.maxValue];
				self.info.weight = parseFloat(e.maxValue)+5;
				console.log(this.rangeValues);
			},
			
			getMainData() {
				const self = this;
				const postData = {};
				postData.searchItem = {
					thirdapp_id: 2,
					city_id:uni.getStorageSync('city_id')
				};
				postData.getBefore = self.$Utils.cloneForm(self.getBefore);
				postData.getAfter = {
					param2:{
						tableName:'Param',
						middleKey:'status',
						key:'status',
						searchItem:{
							city_id:uni.getStorageSync('city_id'),
							
							fee_type:2,
							type:self.type,
						},
						condition:'=',
						order:{
							weight:'asc'
						}
					},
				};
				postData.order = {
					listorder: 'desc'
				};
				const callback = (res) => {
					if (res.info.data.length > 0) {
						self.mainData.push.apply(self.mainData, res.info.data);
						self.info = self.mainData[0];
						self.info.value = self.moneyDate[0];
						/* if(self.mainData[0].param2.length>0){
							this.rangeValues = [0, self.mainData[0].param2[0].weight];
							self.info.weight = self.mainData[0].param2[0].weight;
						} */
						self.info.weight = 5
					} else {
						self.$Utils.showToast('没有更多了', 'none');
					};
					console.log('self.mainData', self.mainData)
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.productGet(postData, callback);
			},
			
			seltSpecs(index){
				const self = this;
				self.seltCurr=index;
				self.info = self.mainData[self.seltCurr];
				self.info.value = self.moneyDate[0];
				self.rangeValues = [0, 0];
				self.info.weight = 5
				//self.info.goodsInfo.value = self.moneyDate[self.seltCurrTwo];
			},
			
			seltSpecsTwo(index){
				const self = this;
				self.seltCurrTwo=index;
				//self.info.goodsInfo = self.mainData[self.seltCurr];
				self.info.value = self.moneyDate[self.seltCurrTwo];
			},
			
			submit(){
				const self = this;
				console.log(213)
				if(!self.info.title){
					self.$Utils.showToast('物品类型异常，请重试', 'none');
					return
				};
				if(JSON.stringify(self.info)=='{}'){
					self.$Utils.showToast('无物品类型', 'none');
					return
				};
				if(!self.info.weight){
					self.$Utils.showToast('请选择重量', 'none');
					return
				};
				
				uni.setStorageSync(self.name, self.info);
				if(self.name=='goodsInfo'){
					self.Router.navigateTo({route:{path:'/pages/order_qusong/order_qusong'}})
				}else if(self.name=='sdGoodsInfo'){
					self.Router.navigateTo({route:{path:'/pages/order_sameDay/order_sameDay'}})
				}
			},
				
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/page.css";
	.wpMsgBox .title{padding: 20px 0 10px 0; color: #666;}
	.wpMsgBox .item{ display: flex; flex-wrap: wrap;}
	.wpMsgBox .item span{width: 31.3%; margin-right: 3%;text-align: center; border: 1px solid #E1E1E1; line-height: 28px;height: 30px;box-sizing: border-box;margin-bottom: 15px; font-size: 12px;}
	.wpMsgBox .item span:nth-of-type(3n){margin-right: 0;}
	.wpMsgBox .item span.on{border-color: #2FA0ED;color: #2FA0ED;}
	.weightRange{width: 70%; height: 1px;margin: 30px auto; position: relative;}
	.weightRange .item{position: absolute; top: -12px;left: -26px; display: flex;flex-direction: column;text-align: center; font-size: 12px; color: #999;}
	.weightRange .yuan{width: 24px; height: 24px;border: 1px solid #e1e1e1; border-radius: 50%; display: block;margin: 0 auto 5px auto;background: #FFF;}
</style>