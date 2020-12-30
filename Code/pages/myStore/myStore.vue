<template>
	<view>
		<view class="cu-custom ">
			<cu-custom :isBack="true" bgColor="bg-gradual-orange">
				<block slot="content">我的发布</block>
				<block slot="backText">返回</block>
			</cu-custom>
		</view>
		<view class="cu-card article" v-for="(item,index) in myStoreList" :key="item._id">
			<view class="cu-item shadow" @click="goToDetail(item)">
				<view class="content">
					<image :src="item.imgList[0]" mode="aspectFill"></image>
					<view class="desc">
						<view class="text-lg text-black m-title">
							{{item.title}}
						</view>
						<view class="text-content"> {{item.description}}</view>
						<view class='container_price text-gray text-sm padding-bottom-xs'>
							<text class='container_price_text_0 text-orange text-lg'>￥{{item.price}}</text>
							<text class="cuIcon-attention text-grey">{{item.views}}</text>
							<text class="cuIcon-message text-grey">{{item.commentsCount}}</text>
							<text class="text-blue">{{item.status}}</text>
						</view>
						<view class='flex justify-between'>
							<view class=" cu-tag bg-yellow light md round padding-lr-sm" v-if="item.status=='已上架'" @click.stop="update(item)">修改</view>
							<view class=" cu-tag bg-green light md round padding-lr-sm" v-if="item.status=='已上架'" @click.stop="done(item._id)">下架</view>
							<view class=" cu-tag bg-red light md round padding-lr-sm" @click.stop="deleteStore(item._id,item.imgList,item.openid)">删除</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="cu-load over" v-if="!loadmoreShow"></view>
		<l-loadmore :show="loadmoreShow" :type="loadmoretype" loading-text="努力加载中~" />
		<view class='goTop' @click="goTop">
			<image src='https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/580b4200-2728-11eb-b680-7980c8a877b8.png'
			 v-if="!showTop"></image>
		</view>
		<l-dialog />
	</view>
</template>

<script>
	export default {
		data() {
			return {
				myStoreList: '',
				showTop: true,
				loadmoreShow: true,
				loadmoretype: 'loading', //end 我是有底线的
				pullDownLimit: 5,
				skip: 1
			};
		},
		methods: {
			// 修改商品
			update(item) {
				console.log("修改")
				uni.navigateTo({
					url: '../update/update?data=' + encodeURIComponent(JSON.stringify(item))
				})
			},
			// 下架商品
			done(storeId) {
				const that = this
				console.log("下架", storeId)
				// 下架
				wx.lin.showDialog({
					type: "confirm",
					content: "确定下架吗？",
					showTitle: false,
					success: (res) => {
						if (res.confirm) {
							uni.showLoading({
								title: '下架中...',
								mask: true
							})
							uniCloud.callFunction({
								name: 'updateStatus',
								data: {
									status: "已下架",
									_id: storeId

								}
							}).then(res => {
								console.log("下架成功")
								uni.showToast({
									title: "下架成功"
								})
								// 传输数据
								that.myStoreList.forEach(item => {
									if (item._id === storeId) {
										item.status = "已下架"
									}
								})
							})

						}
					}
				})

			},
			// 删除商品
			deleteStore(storeId, imgList,myOpenid) {
				const that = this
				console.log("删除imgList", imgList)
				wx.lin.showDialog({
					type: "confirm",
					content: "确定删除该商品吗？",
					showTitle: false,
					success: (res) => {
						if (res.confirm) {
							uni.showLoading({
								title: '删除中，请稍后...',
								mask: true
							})
							uniCloud.callFunction({
								name: 'deleteStore',
								data: {
									_id: storeId
								}
							}).then(res => {
								console.log("删除成功")
								uni.showToast({
									title: "删除成功"
								})
								// 传输数据
								let newStoreList = []
								that.myStoreList.forEach(item => {
									if (!item) {
										that.loadmoreShow = false
									}
									if (item._id !== storeId) {
										newStoreList.push(item)
									}
								})
								that.myStoreList = newStoreList
								uni.$emit("changeStoreCount",newStoreList.length)
							})
							//删除云存储的图片
							uniCloud.callFunction({
								name: 'deleteImages',
								data: {
									imgList: imgList
								}
							}).then(res => {
								console.log("删除成功", res)
							})
							// 删除评论的内容
							uniCloud.callFunction({
								name: 'deleteQuestionTableByStoreid',
								data: {
									storeID: storeId
								}
							}).then(res => {
								console.log("评论删除成功", res)
							})
							// 删除otherComment的内容
							uniCloud.callFunction({
								name:'deleteOtherCommentByStoreid',
								data:{
									storeID:storeId
								}
							}).then(res=>{
								console.log("deleteOtherCommentByStoreid",res)
							})
							// 删除收藏的内容
							uniCloud.callFunction({
								name: 'deleteStoreSaveByStoreid',
								data: {
									storeId: storeId
								}
							}).then(res => {
								console.log("收藏内容删除成功", res)
							})
						}
					}
				})
			},
			// 跳转详情页
			goToDetail(item) {
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(item))
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
			uni.$on('myPublish', data => {
				console.log("接收到的数据", data)
				if (data == "") {
					this.myStoreList = ""
					this.loadmoreShow = false
				} else {
					if(data.length<5){
						this.loadmoretype = 'end'
					}
					this.myStoreList = data
				}
			})
			uni.$on('changeViews', data => {
				this.myStoreList.forEach(item => {
					if (item._id === data.storeId) {
						item.views = data.views
					}
				})
			})
			uni.$on('changeComemntCount', data => {
				this.myStoreList.forEach(item => {
					if (item._id === data.storeId) {
						item.commentsCount = data.Count
					}
				})
			})
			// 接收修改过后传递的信息
			uni.$on("changeDetail", data => {
				console.log("data", data)
				this.myStoreList.forEach(item => {
					if (item._id === data._id) {
						item.price = data.price
						item.phone = data.phone
						item.weixin = data.weixin
						item.description = data.description
						item.connection = data.connection
						item.isFree = data.isFree
					}
				})
			})
		},
		onUnload() {
			uni.$off('myPublish')
			uni.$off('changeViews')
			uni.$off('changeComemntCount')
			uni.$off('changeDetail')
		},

		// 实现上拉加载
		onReachBottom() {
			const that = this;
			let oldData = []
			// console.log(that.navSelected)
			uni.getStorage({
				key: 'openid',
				success(res) {
					uniCloud.callFunction({
						name: 'getUserPublishStoreByOpenid',
						data: {
							openid: res.data,
							"skip": that.skip,
							"pullDownLimit": that.pullDownLimit,
						}
					}).then(res => {
						if (res.result.data.length == 0) {
							that.loadmoretype = "end"
							// console.log("that.skip", that.skip)
							oldData = []
							return;
						} else {
							let oldData = that.myStoreList
							let newData = res.result.data
							let commitData = [...oldData, ...newData]
							that.myStoreList = commitData
							that.skip += 1
							if (that.loadmoretype == "end") {
								that.skip = 1
							}
						}
					})
				}
			})
		},
	}
</script>

<style lang="scss">
	.cu-card.article>.cu-item .content {
		padding: 6rpx 15rpx;
	}

	.text-content {
		line-height: 50rpx;
	}

	.cu-card.article>.cu-item .content .text-content,
	.m-title {
		height: 50rpx;
		width: 450rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.cu-card>.cu-item {
		margin: 10rpx;
	}

	.cu-card.article>.cu-item {
		padding: 20rpx 0rpx;
	}
</style>
