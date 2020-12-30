<template>
	<view>
		<view class="hd">
			<open-data class='logo' type="userAvatarUrl" :style="{bottom:platForm=='ios'?bottom200:bottom119}"></open-data>
		</view>
		<view v-if="!isLogin">
			<view class="padding">
				<view class="box text-center">
					<button class="cu-btn bg-gradual-black bg-black shadow round lg" style="bottom:-800rpx;width:300rpx" @click="onEnter">
						进入</button>
				</view>
			</view>
			<view class="padding">
				<view class="box text-center">
					<button class="cu-btn bg-gradual-black bg-black shadow round lg" style="bottom:-800rpx;width:300rpx" @click="onRegister">
						登录</button>
				</view>
			</view>
		</view>
		<view v-else>
			<view class="padding">
				<view class="box text-center">
					<button class="cu-btn bg-gradual-black bg-black shadow round lg" style="bottom:-730rpx;width:300rpx" @click="onEnter">
						直接进入
						</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				platForm: '',
				bottom119: '',
				bottom200: '',
				isLogin:false
			}
		},
		methods: {
			// 1、进入商品主页
			onEnter() {
				uni.switchTab({
					url:'../home/home'
				})
			},
			// 2、进入登录页面
			onRegister() {
				uni.navigateTo({
					url: '../login/login',
				})
			},
		},
		beforeCreate() {
			const that = this
			uni.getStorage({
				key:'openid',
				success(res){
					console.log(res)
					that.isLogin=true
				}
			})

		},
		created() {
			if (uni.getSystemInfoSync().platform == "ios") {
				this.platForm = 'ios'
				this.bottom200 = '200rpx'
			}
			if (uni.getSystemInfoSync().platform == "android") {
				this.platForm = 'android'
				this.bottom119 = '119rpx'
			}
		},
		async onShow() {
			getApp().globalData.unReadCount=0
			// 获取未读的评论数量
			await uni.getStorage({
				key: 'openid',
				 success(RES) {
					// 获取到我发布的所有商品
					uniCloud.callFunction({
						name: 'getUserPublishStoreByOpenid',
						data: {
							openid: RES.data
						}
					}).then(res => {
						// 拿到了当前用户发布的所有商品
						if (res.result.data.length != 0) {
							res.result.data.forEach(function(v, i) {
								// 拿到当前用户的商品id，去请求questionTable
								uniCloud.callFunction({
									name: 'getQuestionTable',
									data: {
										store_id: v._id
									}
								}).then(res => {
									console.log("接收到我所有商品的评论", res.result.data, RES.data)
									// 判断所有的商品是不是都有评论，有评论的拿出来
									if (res.result.data.length != 0) {
										res.result.data.forEach(function(v, i) {
											if (v.clickOpenid != RES.data && v.read === false) { //判断评论表是不是自己评论的
												getApp().globalData.unReadCount = getApp().globalData.unReadCount+1
											}
											//判断当前商品的评论中是不是有回复
											if (v.children.length != 0) {
												v.children.forEach(function(val, ind) {
													// console.log(val.replyOpenID,RES.data)
													if (val.replyOpenID != RES.data && val.read === false) { //判断回复表是不是自己回复的
														getApp().globalData.unReadCount = getApp().globalData.unReadCount+1
													}
												})
											}
										})
									}
								})
							})
						}else{
							uniCloud.callFunction({
								name: 'getOtherCommentByOpenid',
								data: {
									openid: RES.data
								}
							}).then(res => {
								if(res.result.data.length!=0){
									if (res.result.data[0].commentsList.length != 0) {
										res.result.data[0].commentsList.forEach(el => {
											if (el.read === false) {
												getApp().globalData.unReadCount = getApp().globalData.unReadCount+1
											}
										})
									}
								}
								// 更改我的person页面中的我的消息中的显示多少条未读的数量的
								if (getApp().globalData.unReadCount > 0) {
									uni.showTabBarRedDot({
										index: 2
									})
								}
							})
						}
					})
		
				}
			})
		}
	}
</script>

<style lang="scss">
	/* miniprogram/pages/index/index.wxss */
	page {
		overflow-y: hidden;
		background-image: url(https://6472-dreamland2-a708ef-1259161827.tcb.qcloud.la/bg-image/index-bg.PNG?sign=63ba1ec84f43d4d6049a01307e8a4f5a&t=1603625990);
		background-repeat: no-repeat;
		background-size: 100% 100%;
		-moz-background-size: 100% 100%;
	}

	.hd {
		position: absolute;
		top: 400rpx;
		left: 50%;
		width: 1000rpx;
		margin-left: -500rpx;
		height: 200rpx;
		transition: all .35s ease;
	}

	.logo {
		display: block;
		overflow: hidden;
		position: absolute;
		z-index: 2;
		left: 50%;
		bottom: 200rpx;
		width: 160rpx;
		height: 160rpx;
		margin-left: -80rpx;
		border-radius: 160rpx;
		animation: sway 10s ease-in-out infinite;
		opacity: .95;
	}

	@keyframes sway {
		0% {
			transform: translate3d(0, 20rpx, 0) rotate(-15deg);
		}

		17% {
			transform: translate3d(0, 0rpx, 0) rotate(25deg);
		}

		34% {
			transform: translate3d(0, -20rpx, 0) rotate(-20deg);
		}

		50% {
			transform: translate3d(0, -10rpx, 0) rotate(15deg);
		}

		67% {
			transform: translate3d(0, 10rpx, 0) rotate(-25deg);
		}

		84% {
			transform: translate3d(0, 15rpx, 0) rotate(15deg);
		}

		100% {
			transform: translate3d(0, 20rpx, 0) rotate(-15deg);
		}
	}
</style>
