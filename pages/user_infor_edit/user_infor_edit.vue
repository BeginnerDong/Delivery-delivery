<template>
	<div>
		<!-- 标题 -->
		<div class="T-head line50 center fs16 Tfix-head">
			<a class="back-next pa" href="javascript:history.go(-1);"><img class="icon" src="../../static/images/backIcon.png" ></a>
			添加信息
		</div>
		<div class="h50"></div>
		
		<div class="userInfor">
			<form action="" method="">
				<ul>
					<li>
						<span class="name">姓名：</span>
						<input class="edit" type="text" value="张三" placeholder="请输入您的姓名" >
					</li>
					<li>
						<span class="name">电话：</span>
						<input class="edit" type="text" value="15632564653" placeholder="请输入您的电话" onkeyup="this.value=this.value.replace(/\D/g,'')"  maxlength="11">
					</li>
					<li>
						<span class="name">地址：</span>
						<input class="edit" type="text" value="陕西省西安市高新区大都荟" placeholder="请输入您的常用地址">
					</li>
				</ul>
			</form>
		</div>
		
		<div class="submitbtn" style="margin-top: 80px;">
			<a class="btn" href="#">确认</a>
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
</style>	



