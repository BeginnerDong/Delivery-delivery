<template>
	<div>
		<div class="xqTex_infor pdlr4">
			<div class="center line40 pdt10">{{mainData.title}}</div>
			<view class="content ql-editor" v-html="mainData.content">
			</view>
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
			self.id = options.id;
			self.paginate = self.$Utils.cloneForm(self.$AssetsConfig.paginate);
			self.$Utils.loadAll(['getMainData'], self);
		},

		methods: {

			getMainData() {
				const self = this;
				console.log('852369')
				const postData = {};
				postData.searchItem = {
					thirdapp_id:2
				};
				postData.getBefore = {
					caseData: {
						tableName: 'Label',
						searchItem: {
							title: ['=', ['下单须知']],
						},
						middleKey: 'menu_id',
						key: 'id',
						condition: 'in',
					},
				};
				const callback = (res) => {
					if (res.solely_code == 100000 && res.info.data[0]) {
						self.mainData = res.info.data[0];

						const regex = new RegExp('<img', 'gi');
						self.mainData.content = self.mainData.content.replace(regex, `<img style="max-width: 100%;"`);
					} else {
						self.$Utils.showToast(res.msg, 'none')
					};
					self.$Utils.finishFunc('getMainData');
				};
				self.$apis.articleGet(postData, callback);
			},
		}
	}
</script>


<style>
	@import "../../assets/style/public.css";
</style>