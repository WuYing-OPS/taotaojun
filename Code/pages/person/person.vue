<template>
	<view>
		<scroll-view scroll-y class="scrollPage" :style="showMyMessage ? 'height: 100vh' : ''">
			<view class="UCenter-bg">
				<image :src="userMessage.avatarUrl" class="png round" mode="widthFix" @click="login"></image>
				<view class="text-xl margin-top text-black" @click="login">
					{{userMessage.name}}
				</view>
				<view class="margin-top-sm" @click="login">
					<text class="text-black">{{userMessage.class}}</text>
				</view>
				<image src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/52812180-24bf-11eb-9dfb-6da8e309e0d8.gif"
				 mode="scaleToFill" class="gif-wave"></image>
			</view>
			<view class="padding flex text-center text-grey bg-white shadow-warp">
				<view class="flex flex-sub flex-direction solid-right" @click="onEnterHomeSave">
					<view class="text-xxl text-blue">{{userSaveStoreCount}}</view>
					<view class="margin-top-sm">
						总收藏数</view>
				</view>
				<view class="flex flex-sub flex-direction" @click="onEnterHomePublish">
					<view class="text-xxl text-green">{{publishStoreCount}}</view>
					<view class="margin-top-sm">
						总发布商品数</view>
				</view>
			</view>
			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg radius">
				<!-- <view class="cu-item arrow" @click="onAuthReceiveMsg">
					<view class="content" hover-class="none">
						<text class="cuIcon-forwardfill text-green"></text>
						<text class="text-grey">接受新消息推送一次</text>
					</view>
				</view> -->
				<view class="cu-item arrow" @click="onEnterHomeUserInfo">
					<view class="content" hover-class="none">
						<text class="cuIcon-peoplefill text-orange"></text>
						<text class="text-grey">我的信息</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeComment">
					<view class="content " hover-class="none">
						<text class="cuIcon-commentfill text-red"></text>
						<text class="text-grey">我的消息</text>
					</view>
					<view class="action">
						<view class="cu-tag round bg-red sm" v-if="myUnReadCount">{{myUnReadCount}}</view>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeSave">
					<view class="content" hover-class="none">
						<text class="cuIcon-favorfill text-orange"></text>
						<text class="text-grey">我的收藏</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomePublish">
					<view class="content" hover-class="none">
						<text class="cuIcon-formfill text-blue"></text>
						<text class="text-grey">我发布的商品</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="copyLink" data-link="https://github.com/2horse9sun/University_O2O">
					<view class="content">
						<text class="cuIcon-github text-grey"></text>
						<text class="text-grey">复制二货小程序的GitHub地址</text>
					</view>
				</view>
				<view class="cu-item arrow">
					<button class="cu-btn content" open-type="feedback">
						<text class="cuIcon-writefill text-cyan"></text>
						<text class="text-grey">意见反馈</text>
					</button>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeAbout">
					<view class="content" hover-class="none">
						<text class="cuIcon-infofill text-orange"></text>
						<text class="text-grey">关于二货</text>
					</view>
				</view>

			</view>
			<view class="cu-tabbar-height"></view>
		</scroll-view>
		<view class="hline"></view>
		<!--确认框 -->
		<l-dialog :show="show"></l-dialog>
		<l-mask :show="showMyMessage" opacity="0.3" center locked>
			<my-message :userInfo="userMessage" />
		</l-mask>
	</view>
</template>

<script>
	import MyMessage from '../../components/uni-myMessage/uni-myMessage.vue'
	export default {
		components: {
			MyMessage
		},
		data() {
			return {
				myUnReadCount: 0,
				showMyMessage: false, //展示我的个人信息
				show: false,
				openid: "", //openid
				publishStoreCount: 0, //用户发布的商品数
				userSaveStoreCount: 0, //用户总收藏的商品数
				userMessage: {
					avatarUrl: 'https://6472-dreamland2-a708ef-1259161827.tcb.qcloud.la/bg-image/default-avatar.PNG',
					name: '立即登录',
					class: "登录后可发布信息"
				}
			};
		},
		methods: {
			// 登录验证的弹出框哦
			isShowDialogLogin(content) {
				this.show = true
				wx.lin.showDialog({
					type: "confirm",
					title: "温馨提示",
					content: content,
					success: (res) => {
						if (res.confirm) {
							uni.navigateTo({
								url: '../login/login'
							})
						} else if (res.cancel) {
							console.log('用户点击取消')
						}
					}
				})
			},
			// 登录
			login() {
				const that = this;
				uni.getStorage({
					key: 'openid',
					fail() {
						that.isShowDialogLogin("前往登录页面")
					}
				})
			},
			loginLook() {
				const that = this
				uni.getStorage({
					key: 'openid',
					fail() {
						that.isShowDialogLogin("登录即可查看")
					},
					success() {
						that.openid = true
					}
				})
			},
			// 查看个人信息
			onEnterHomeUserInfo() {
				this.loginLook()
				if (this.openid) {
					console.log("login")
					this.showMyMessage = true
				}
			},
			// 我的消息
			async onEnterHomeComment() {
				const that = this
				this.loginLook()
				if (this.openid) {
					uni.navigateTo({
						url: '../myMessage/myMessage'
					})
				}
				uni.getStorage({
					key: 'openid',
					success(res) {
						that.openid = res.data
					}
				})
				await uniCloud.callFunction({
					name: 'getUserPublishStoreByOpenid',
					data: {
						openid: that.openid
					}
				}).then(res => {
					console.log("接收到我发布的商品", res.result.data)
					// 拿到了当前用户发布的所有商品
					console.log(res.result)
					if (res.result.data.length != 0) {
						res.result.data.forEach(function(v, i) {
							// 拿到当前用户的商品id，去请求questionTable
							uniCloud.callFunction({
								name: 'getQuestionTable',
								data: {
									store_id: v._id
								}
							}).then(res => {
								console.log("接收到我所有商品的评论", res.result.data, that.openid)
								// 判断所有的商品是不是都有评论，有评论的拿出来
								if (res.result.data.length != 0) {
									let myCommentList = []
									res.result.data.forEach(function(v, i) {
										if (v.clickOpenid != that.openid) { //判断评论表是不是自己评论的
											console.log("parent", v)
											myCommentList.push(v)
										}
										//判断当前商品的评论中是不是有回复
										if (v.children.length != 0) {
											v.children.forEach(function(val, ind) {
												console.log("children", val)
												if (val.replyOpenID != that.openid) { //判断回复表是不是自己回复的
													myCommentList.push(val)
												}
											})
										}
									})
									uniCloud.callFunction({
										name: 'getOtherCommentByOpenid',
										data: {
											openid: that.openid
										}
									}).then(res => {
										// 将两个数组中的对象进行合并
										console.log(myCommentList, res)
										let newMyCommentList
										if (res.result.data.length != 0) {
											newMyCommentList = [...myCommentList, ...res.result.data[0].commentsList]
										} else {
											newMyCommentList = [...myCommentList]
										}
										if (newMyCommentList.length != 0) {
											uni.$emit("myCommentList", newMyCommentList)
										} else {
											uni.$emit("myCommentList", "")
										}
									})
								}
							})
						})
					} else {
						uniCloud.callFunction({
							name: 'getOtherCommentByOpenid',
							data: {
								openid: that.openid
							}
						}).then(res => {
							// 将两个数组中的对象进行合并
							console.log(myCommentList, res)
							let newMyCommentList
							let myCommentList = []
							if (res.result.data.length != 0) {
								newMyCommentList = [...myCommentList, ...res.result.data[0].commentsList]
							} else {
								newMyCommentList = [...myCommentList]
							}
							if (newMyCommentList.length != 0) {
								uni.$emit("myCommentList", newMyCommentList)
							} else {
								uni.$emit("myCommentList", "")
							}
						})
					}

				})

			},
			// 我的收藏
			async onEnterHomeSave() {
				if (this.openid) {
					uni.navigateTo({
						url: '../collect/collect'
					})
				}
				const that = this
				this.loginLook()
				// 得到所有收藏的商品
				await uni.getStorage({
					key: 'openid',
					success(res) {
						console.log("res.data", res.data)
						uniCloud.callFunction({
							name: "getAllStoreSave",
							data: {
								openid: res.data
							}
						}).then(res => {
							let store = []
							if (res.result.data.length === 0) {
								uni.$emit('getAllSave', "")
							}
							console.log("收藏的商品", res)
							res.result.data.forEach(async (el, index) => {
								console.log(el.storeId)
								await uniCloud.callFunction({
									name: 'getStoreDetail',
									data: {
										store_id: el.storeId
									}
								}).then(RES => {
									console.log(RES.result.data.length)
									store.push(RES.result.data[0])
									if (store.length === res.result.data.length) {
										setTimeout(function() {
											uni.$emit('getAllSave', store)
										}, 100)
									}
								})
							})
						})
					}
				})
			},
			// 我发布的商品
			async onEnterHomePublish() {
				const that = this
				if (this.openid) {
					uni.navigateTo({
						url: '../myStore/myStore'
					})
				}
				this.loginLook()
				// 得到所有我发布的商品
				await uni.getStorage({
					key: 'openid',
					success(res) {
						console.log("res.data", res.data)
						uniCloud.callFunction({
							name: 'getUserPublishStoreByOpenid',
							data: {
								openid: res.data,
								skip: 0,
								pullDownLimit: 5
							}
						}).then(res => {
							console.log("我发布的商品", res)
							if (res.result.data.length == 0) {
								uni.$emit("myPublish", "")
							} else {
								uni.$emit("myPublish", res.result.data)
							}
						})
					}
				})
			},
			// 点击复制小程序地址
			copyLink(e) {
				this.loginLook()
				uni.setClipboardData({
					data: e.currentTarget.dataset.link
				})
			},
			// 关于此小程序
			onEnterHomeAbout() {
				uni.navigateTo({
					url: '../about/about'
				})
			},
		},
		beforeCreate() {
			const that = this
			console.log('App Show')
			uni.getStorage({
				key: 'openid',
				success(res) {
					// 得到个人信息
					uniCloud.callFunction({
						name: 'getUserInfoByOpenid',
						data: {
							openid: res.data
						}
					}).then(res => {
						that.userMessage = res.result.data[0]
					})
					//得到用户发布商品的总数哦
					uniCloud.callFunction({
						name: 'getUserPublishStoreByOpenid',
						data: {
							openid: res.data
						},
					}).then(res => {
						that.publishStoreCount = res.result.data.length
					})
					// 得到用户收藏的商品的总数
					uniCloud.callFunction({
						name: 'getAllStoreSave',
						data: {
							openid: res.data
						},
					}).then(res => {
						that.userSaveStoreCount = res.result.data.length
					})
				}
			})
		},
		async onShow() {
			const that = this
			if (getApp().globalData.unReadCount === 0) {
				uni.hideTabBarRedDot({
					index: 2
				})
			}
			if(getApp().globalData.unReadCount>=0){
				this.myUnReadCount = getApp().globalData.unReadCount
			}
			uni.getStorage({
				key: 'openid',
				success(res) {
					that.openid = res.data
				}
			})
			if (this.publishStoreCount == 0) {
				await uni.getStorage({
					key: 'openid',
					success(res) {
						// 得到用户发布商品的总数哦
						uniCloud.callFunction({
							name: 'getUserPublishStoreByOpenid',
							data: {
								openid: res.data
							},
						}).then(res => {
							that.publishStoreCount = res.result.data.length
						})
					}
				})
			}
			if (this.userSaveStoreCount == 0) {
				await uni.getStorage({
					key: 'openid',
					success(res) {
						// 得到用户收藏商品的总数哦
						uniCloud.callFunction({
							name: 'getAllStoreSave',
							data: {
								openid: res.data
							},
						}).then(res => {
							that.userSaveStoreCount = res.result.data.length
						})
					}
				})
			}
		},
		onLoad() {
			if (getApp().globalData.userInfo) {
				this.userMessage = getApp().globalData.userInfo
				console.log("userinfo", getApp().globalData.userInfo)
			}
		},
		created() {
			console.log("created")
			uni.$on('changeStoreCount', data => {
				console.log(data)
				this.publishStoreCount = data
			})
			uni.$on('hideMask', data => {
				this.showMyMessage = data
			})
			uni.$on('userInfo', data => {
				this.userMessage = data.data
				console.log("uuuuuuu", data)
			})
			uni.$on('changeSaveCount', data => {
				console.log("da鹅鹅鹅鹅鹅鹅饿鹅鹅鹅饿ta", data)
				this.userSaveStoreCount = data
			})
		},
		onUnload() {
			uni.$off('changeStoreCount')
			uni.$off('hideMask')
			uni.$off('userInfo')
			uni.$off('changeSaveCount')
		}
	}
</script>

<style lang="scss">
	.UCenter-bg {
		background-image: url(https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/72320710-2681-11eb-880a-0db19f4f74bb.jpg);
		background-size: cover;
		height: 550rpx;
		display: flex;
		justify-content: center;
		padding-top: 40rpx;
		overflow: hidden;
		position: relative;
		flex-direction: column;
		align-items: center;
		color: #fff;
		font-weight: 300;
		text-shadow: 0 0 3px rgba(0, 0, 0, 0.3);
	}

	.UCenter-bg text {
		opacity: 0.8;
	}

	.UCenter-bg image {
		width: 200rpx;
		height: 200rpx;
	}

	.UCenter-bg .gif-wave {
		position: absolute;
		width: 100%;
		bottom: 0;
		left: 0;
		z-index: 99;
		mix-blend-mode: screen;
		height: 100rpx;
	}

	map,
	.mapBox {
		left: 0;
		z-index: 99;
		mix-blend-mode: screen;
		height: 100rpx;
	}

	map,
	.mapBox {
		width: 750rpx;
		height: 300rpx;
	}


	.bar-wrapper {
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
	}

	.hline {
		margin-bottom: 120rpx;
	}
</style>
