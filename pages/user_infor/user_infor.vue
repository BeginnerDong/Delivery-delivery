<template>
	<div style="padding-bottom: 50px;">
		<div class="T-head line50 center fs16 Tfix-head">
			<a class="back-next pa" href="javascript:history.go(-1);"><img class="icon" src="../../static/images/backIcon.png" ></a>
			基本信息
		</div>
		<div class="h50"></div>
		
		<div class="addressLis">
			<ul>
				<li v-for="(item,index) in informsgLis" :key="index">
					<h1 class="name">张三 <em class="phone">15632564653</em></h1>
					<p class="adrs">陕西省西安市高新区大都荟</p>
					
					<div class="right">
						<a class="tt" href="user_infor_edit.html">
							<img src="../../static/images/address-icon.png" alt="">
							<span>编辑</span>
						</a>
						<div class="tt">
							<img src="../../static/images/address-icon1.png" alt="">
							<span>删除</span>
						</div>
					</div>
				</li>
			</ul>
		</div>
		
		<div class="submitbtn" style="margin-top: 80px;">
			<a class="btn" href="user_infor_add.html">添加信息</a>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				couponDate:[
					{},{},{}
				]
			}
		},

		onLoad() {
			const self = this;
		},

		methods: {
			
		},

	};
</script>

<style>
	@import "../../assets/style/public.css";
	@import "../../assets/style/address.css";
	page{
		background: #F5F5F5;
	}
</style>	



