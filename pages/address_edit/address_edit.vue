<template>
	<div>
		<div class="addressAdd bgwhite">
			<ul>
				<li>
					<div class="title">联系人姓名</div>
					<div class="rightMsg">张丹</div>
				</li>
				<li>
					<div class="title">手机号码</div>
					<div class="rightMsg">15689232356</div>
				</li>
				<li>
					<div class="title">所在地区</div>
					<div class="rightMsg">陕西省西安市长安区</div>
				</li>
				<li>
					<div class="title">详细地址</div>
					<div class="rightMsg">韦曲街道</div>
				</li>
			</ul>
		</div>
		
		<div class="submitbtn" style="margin-top: 130px;">
			<a class="btn" href="address.html">确定</a>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				is_show:false,
				mainData: []
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
</style>