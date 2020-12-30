<template>
	<view>
		<view class="cu-custom ">
			<cu-custom :isBack="true" bgColor="bg-gradual-orange">
				<block slot="backText">返回</block>
				<block slot="content">收藏</block>
			</cu-custom>
		</view>
		<view class="cu-list menu-avatar comment solids-top">
			<view class="cu-item" v-for="(item,index) in saveStoreList" :key="item._id" @click="goToDetail(item)">
				<view class="cu-avatar round" :style="{backgroundImage:`url(${item.avatarUrl})`}"></view>
				<view class="content">
					<view class="text-grey">{{item.nickName}}</view>
					<view class="text-gray text-content text-df">
						{{item.title}}
					</view>
					<view class="bg-grey padding-sm radius margin-top-sm  text-sm">
						<view class="flex">
							<view class="flex-sub">{{item.description}}</view>
						</view>
					</view>
					<view class="margin-top-sm flex justify-between">
						<view class="text-gray text-df">{{item.ctime}}</view>
						<view @click.stop="deleteSave(item._id)">
							<text class="text-orange">取消收藏</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<l-loadmore :show="loadmoreShow" :type="loadmoretype" loading-text="努力加载中~" />
		<view class="cu-load  over" v-if="!loadmoreShow"></view>
		<view class='goTop' @click="goTop">
			<image src='https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/580b4200-2728-11eb-b680-7980c8a877b8.png'
			 v-if="!showTop"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loadmoreShow: true,
				loadmoretype: 'loading', //end 我是有底线的
				showTop: true,
				saveStoreList: '', //收藏的所有商品列表
			};
		},
		methods: {
			// 对获取到的数据进行时间的处理
			handleTime(context) {
				function getTime(shijianchuo) {
					let time = new Date(shijianchuo)
					const y = time.getFullYear();
					const m = time.getMonth() + 1;
					const d = time.getDate();
					const h = time.getHours();
					const mm = time.getMinutes();
					const s = time.getSeconds();
					return y + '-' + m + '-' + d + ' ' + h + ':' + m + ':' + s;
				}
				for (var i = 0; i < context.length; i++) {
					let newTime = getTime(context[i].ctime)
					context[i].ctime = newTime
				}
				this.saveStoreList = context
			},
			// 跳转详情页
			goToDetail(item) {
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(item))
				})
			},
			// 取消收藏哦
			deleteSave(storeid) {
				console.log(storeid)
				const that = this
				uni.getStorage({
					key: 'openid',
					success(Mopenid) {
						uni.showModal({
							content: '确定取消收藏吗',
							success(res) {
								if (res.confirm) {
									uni.showLoading({
										title: "取消中...",
										mask: true
									})
									uniCloud.callFunction({
										name: 'deleteStoreSave',
										data: {
											storeId: storeid,
											userOpenid: Mopenid.data
										}
									}).then(res => {
										uni.hideLoading()
										console.log("删除", res)
										uniCloud.callFunction({
											name: 'getAllStoreSave',
											data: {
												openid: Mopenid.data
											}
										}).then(res => {
											let store = []
											console.log("收藏的商品", res)
											if(res.result.data.length===0){
												that.saveStoreList = ""
												that.loadmoretype="end"
												that.loadmoreShow =false
											}
											// 获取到收藏的总数
											uni.$emit('changeSaveCount', res.result.data.length)
											res.result.data.forEach(async (el, index) => {
												console.log(el.storeId)
												await uniCloud.callFunction({
													name: 'getStoreDetail',
													data: {
														store_id: el.storeId
													}
												}).then(RES => {
													store.push(RES.result.data[0])
													if (store.length === res.result.data.length) {
														that.handleTime(store)
													}
												})
											})
										})
									})
								}
							}
						})
					}
				})
			},
			// 点击回到顶部
			onPageScroll(e) {
				// this.scrollTop = e.scrollTop
				if (e.scrollTop > 500) {
					this.showTop = false;
				} else {
					this.showTop = true;
				}
			},
			goTop() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				})
			},
		},
		onLoad() {
			uni.$on('getAllSave', data => {
				console.log("data",data)
				if(data==""){
					this.saveStoreList = []
					this.loadmoreShow=false
				}
				this.handleTime(data)
				this.loadmoretype = 'end'
			})
			uni.$on('changeViews', data => {
				this.saveStoreList.forEach(item => {
					if (item._id === data.storeId) {
						item.views = data.views
					}
				})
			})
			uni.$on('changeComemntCount', data => {
				this.saveStoreList.forEach(item => {
					if (item._id === data.storeId) {
						item.commentsCount = data.Count
					}
				})
			})
		},
		onUnload() {
			uni.$off('getAllSave')
			uni.$off('changeViews')
			uni.$off('changeComemntCount')
		},
	}
</script>

<style lang="scss">
</style>
