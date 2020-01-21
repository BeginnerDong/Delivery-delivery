<template>
	<div>
		<!-- 标题 -->
		<div class="T-head line50 center fs16 Tfix-head">
			<a class="back-next pa" href="javascript:history.go(-1);"><img class="icon" src="../../static/images/backIcon.png" ></a>
			编辑银行卡
		</div>
		<div class="h50"></div>
		
		<div class="userInfor">
			<form action="" method="">
				<ul>
					<li>
						<span class="name">持卡人：</span>
						<input class="edit" type="text" value="张丹" placeholder="请输入持卡人的姓名">
					</li>
					<li>
						<span class="name">手机号：</span>
						<input class="edit" type="number" value="15689568897" placeholder="" onkeyup="this.value=this.value.replace(/\D/g,'')"  maxlength="11">
					</li>
					<li>
						<span class="name">开户行：</span>
						<input class="edit" type="text" value="中国银行" placeholder="请输入您的银行卡的开户行">
					</li>
					<li>
						<span class="name">银行卡号：</span>
						<input class="edit" type="number" value="6215525245127853" placeholder="请输入您的银行卡号">
					</li>
				</ul>
			</form>
		</div>
		<div class="submitbtn" style="margin-top: 100px;">
			<button class="btn" href="#">确认</button>
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



