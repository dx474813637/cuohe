<template>
	<view class="u-content u-p-10">
		<u-parse :content="content"></u-parse>
		<view class="u-p-20 u-m-t-30">
			<u-button type="primary" @click="dingyue_save">确 认</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				content: ``,
				list : { }
			};
		},
		async onLoad() {
			const res = await this.$api.dingyue_tishi()
			if(res.code == 1) { 
				this.content = res.list.info
				this.list = res.list 
				uni.setNavigationBarTitle({
					title: this.list.title
				});
			}
		},
		methods: {
			async dingyue_save() {
				const res = await this.$api.dingyue_save()
				if(res.code == 1) { 
					uni.showToast({
						title: res.msg,
						icon: 'none',
						duration: 2000
					})
				}
			}
		}
	}
</script>

<style lang="scss">

</style>
