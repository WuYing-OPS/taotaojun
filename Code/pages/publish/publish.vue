<template>
	<view>
		<view class="cu-custom" :style="{zIndex:(noPublish?'100000':'-1')}">
			<cu-custom bgColor="bg-gradual-orange">
				<block slot="content">发布</block>
				<!-- <block slot="backText" >返回</block> -->
			</cu-custom>
		</view>
			<uni-publish />
	</view>
</template>

<script>
	import uniPublish from '../../components/uni-publish/uni-publish.vue'
	export default {
		data() {
			return {
				noPublish: true
			};
		},
		methods: {
		},
		components: {
			uniPublish
		},
		onShow() {
			console.log("cre")
			const that = this
			uni.getStorage({
				key:'openid',
				fail(){
					that.isShow = true
					uni.showModal({
						title:'温馨提示',
						content:'前往登录页面',
						success(res) {
							if(res.confirm){
								uni.navigateTo({
									url:'../login/login'
								})
							}else{
								uni.switchTab({
									url:'../home/home'
								})
							}
						}
					})
				}
			})
			uni.$on('changeNoPublish', data => {
				this.noPublish = data
			})
		},
		onUnload() {
			uni.$off('changeNoPublish')
		}
	}
</script>

<style lang="scss">

</style>
