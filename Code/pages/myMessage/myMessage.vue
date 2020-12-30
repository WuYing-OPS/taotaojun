<template>
	<view>
		<view class="cu-custom">
			<cu-custom bgColor="bg-gradual-orange" :isBack="true">
				<block slot="content">消息</block>
				<block slot="backText">返回</block>
			</cu-custom>
		</view>
		<view class="flex justify-between bg-white padding">
			<view class="">
				消息列表
			</view>
			<view class="cuIcon-edit text-orange" @click="allRead">
				全部标为已读
			</view>
		</view>

		<scroll-view scroll-x class="bg-white nav margin-top-sm">
			<view class="flex text-center">
				<view class="cu-item flex-sub" :class="index==TabCur?'text-orange cur':''" v-for="(item,index) in navList" :key="index"
				 @tap="tabSelect($event,item)" :data-id="index">
					{{item}}
				</view>
			</view>
		</scroll-view>
		<view class="cu-list menu-avatar margin-top-xs">
			<view class="cu-item " v-for="(item,index) in commentList" :key="index" @click="goToDetail(item.storeID||item.store_id,item)">
				<view v-if="item.isParent" class="cu-avatar radius lg" :style="{backgroundImage:`url(${item.clickAvatarUrl})`}"></view>
				<view v-else class="cu-avatar radius lg" :style="{backgroundImage:`url(${item.replyAvatarUrl})`}"></view>
				<view class="content">
					<view class="text-pink">
						<view class="text-cut">
							<text v-if="item.isParent">{{item.clickNickName}}</text>
							<text v-else>{{item.replyNickName}}</text>
						</view>
					</view>
					<view class="text-gray text-sm flex">
						<view class="text-cut">
							<text v-if="item.isParent">{{item.commentValue}}</text>
							<text v-else-if="item.isChildren">{{item.replyContent}}</text>
							<text v-else>{{item.comment}}</text>
						</view>
					</view>
				</view>
				<view class="action margin-right-sm">
					<view class="text-grey text-xs">{{item.ctime}}</view>
					<view class="cuIcon-title text-orange" v-if="!item.read"></view>
				</view>
			</view>
		</view>
		<view class="cu-load over" v-if="!loadmoreShow"></view>
		<l-loadmore :show="loadmoreShow" :type="loadmoretype" loading-text="努力加载中~" />
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
				showTop: true,
				TabCur: 0,
				scrollLeft: 0,
				navList: ['全部', '未读'],
				commentList: '', //全部消息列表
				loadmoreShow: true,
				loadmoretype: 'loading',
				skip: 1,
				pullDownLimit: 5,
				currentNav: '全部'
			};
		},
		methods: {
			// 跳转详情页
			async goToDetail(storeID, item) {
				const that = this
				console.log("jkshcvsdhvuhsukvdsvsduvuksvshkj", item, that.commentList)
				await uniCloud.callFunction({
					name: 'getStoreDetail',
					data: {
						store_id: storeID
					}
				}).then(res => {
					uni.navigateTo({
						url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(res.result.data[0]))
					})
					// 去除已读的点或者在未读界面去除已读的消息
					setTimeout(function() {
						if (that.currentNav === '未读') {
							that.commentList.forEach((el, i) => {
								if (el._id === item._id) {
									that.commentList.splice(i, 1)
								}
							})
						} else {
							item.read = true
						}
					}, 500)
					// 将当前消息的未读改为已读,判断点击的是哪里的评论
					if (item.isParent) {
						uniCloud.callFunction({
							name: 'updateCommentRead',
							data: {
								questionId: item._id,
								isParent: item.isParent,
								read: true
							}
						}).then(res => {
							if (getApp().globalData.unReadCount > 0) {
								getApp().globalData.unReadCount = getApp().globalData.unReadCount - 1
							} else {
								getApp().globalData.unReadCount = 0
							}
						}).catch(res => {
							console.log("出现异常", res)
						})
					} else if (item.isChildren) {
						uniCloud.callFunction({
							name: 'updateCommentRead',
							data: {
								_id: item._id,
								read: true,
								questionId: item.question_id,
								isChildren: item.isChildren
							}
						}).then(res => {
							if (getApp().globalData.unReadCount > 0) {
								getApp().globalData.unReadCount = getApp().globalData.unReadCount - 1
							} else {
								getApp().globalData.unReadCount = 0
							}
						}).catch(res => {
							console.log("出现异常", res)
						})
					} else {
						uni.getStorage({
							key: 'openid',
							success(RES) {
								console.log("vjsvusuvsuvushudihuifgyuw78y8y8", item._id)
								uniCloud.callFunction({
									name: 'updateCommentRead',
									data: {
										openid: RES.data,
										_id: item._id,
										read: true
									}
								}).then(res => {
									if (getApp().globalData.unReadCount > 0) {
										getApp().globalData.unReadCount = getApp().globalData.unReadCount - 1
									} else {
										getApp().globalData.unReadCount = 0
									}
								}).catch(res => {
									console.log("出现异常", res)
								})
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
			tabSelect(e, item) {
				const that = this
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
				this.currentNav = item
				this.commentList = ''
				this.loadmoreShow = true
				this.loadmoretype = 'loading' 
				uni.getStorage({
					key: 'openid',
					success(RES) {
						uniCloud.callFunction({
							name: 'getUserPublishStoreByOpenid',
							data: {
								openid: RES.data
							}
						}).then(res => {
							console.log("getUserPublishStoreByOpenid",res.result.data)
							if (res.result.data.length != 0) {
								// 拿到了当前用户发布的所有商品
								res.result.data.forEach(function(v, i) {
									// 拿到当前用户的商品id，去请求questionTable
									uniCloud.callFunction({
										name: 'getQuestionTable',
										data: {
											store_id: v._id
										}
									}).then(res => {
										console.log('getQuestionTable',res.result.data)
										// 判断所有的商品是不是都有评论，有评论的拿出来
										if (item === '全部') {
											let myCommentList = []
											if (res.result.data.length != 0) {
												res.result.data.forEach(function(v, i) {
													if (v.clickOpenid != RES.data) { //判断评论表是不是自己评论的
														console.log("parent", v)
														myCommentList.push(v)
													}
													//判断当前商品的评论中是不是有回复
													if (v.children.length != 0) {
														v.children.forEach(function(val, ind) {
															console.log("children", val)
															if (val.replyOpenID != RES.data) { //判断回复表是不是自己回复的
																myCommentList.push(val)
															}
														})
														}
														
														uniCloud.callFunction({
															name: 'getOtherCommentByOpenid',
															data: {
																openid: RES.data
															}
														}).then(res => {
															// 将两个数组中的对象进行合并
															let newMyCommentList
															// 拿出otherComment的read为false的评论
															if (res.result.data.length != 0) {
																newMyCommentList = [...myCommentList, ...res.result.data[0].commentsList]
															} else {
																newMyCommentList = [...myCommentList]
															}
															if (newMyCommentList.length != 0) {
																let commentCount = 0
																let newData = []
																newMyCommentList.forEach(el => {
																	let newTime = that.getTime(el.ctime)
																	el.ctime = newTime
																	if (el.children) {
																		el.children.forEach(el => {
																			let childrenNewTime = that.getTime(el.ctime)
																			el.ctime = childrenNewTime
																		})
																	}
																	newData.push(el)
																	if (el.read === false) {
																		commentCount += 1
																	}
																})
																getApp().globalData.unReadCount = commentCount
																	that.commentList = newData
																	that.loadmoretype = 'end'
															} else {
																that.commentList = ""
																that.loadmoreShow = false
															}
														})
												})
											}

										} else if (item === '未读') {
											let myCommentList = []
											if (res.result.data.length != 0) {
												res.result.data.forEach(function(v, i) {
													if (v.clickOpenid != RES.data && v.read === false) { //判断评论表是不是自己评论的
														myCommentList.push(v)
													}
													//判断当前商品的评论中是不是有回复
													if (v.children.length != 0) {
														v.children.forEach(function(val, ind) {
															if (val.replyOpenID != RES.data && val.read === false) { //判断回复表是不是自己评论的
																myCommentList.push(val)
															}
														})
														}
														uniCloud.callFunction({
															name: 'getOtherCommentByOpenid',
															data: {
																openid: RES.data
															}
														}).then(res => {
															// 将两个数组中的对象进行合并
															console.log(myCommentList, res)
															let newMyCommentList
															// 拿出otherComment的read为false的评论
															let otherCommentList = []

															if (res.result.data.length != 0) {
																res.result.data[0].commentsList.forEach(el => {
																	if (el.read === false) {
																		otherCommentList.push(el)
																	}
																})
																newMyCommentList = [...myCommentList, ...otherCommentList]
															} else {
																newMyCommentList = [...myCommentList]
															}
															getApp().globalData.unReadCount = newMyCommentList.length
															if (newMyCommentList.length != 0) {
																// that.commentList = newMyCommentList
																let newData = []
																newMyCommentList.forEach(el => {
																	let newTime = that.getTime(el.ctime)
																	el.ctime = newTime
																	if (el.children) {
																		el.children.forEach(el => {
																			let childrenNewTime = that.getTime(el.ctime)
																			el.ctime = childrenNewTime
																		})
																	}
																	newData.push(el)
																})
																	that.commentList = newData
																	that.loadmoretype = 'end'
															} else {
																that.commentList = ""
																that.loadmoreShow = false
															}
														})
												})
											}


										}
									})

								})
							} else if (res.result.data.length === 0) {
								if (item === '全部') {
									uniCloud.callFunction({
										name: 'getOtherCommentByOpenid',
										data: {
											openid: RES.data
										}
									}).then(res => {
										// 将两个数组中的对象进行合并
										let newMyCommentList
										// 拿出otherComment的read为false的评论
										if (res.result.data.length != 0) {
											let commentCount = 0
											newMyCommentList = [...res.result.data[0].commentsList]
											newMyCommentList.forEach(el => {
												if (el.read === false) {
													commentCount += 1
												}
											})
											getApp().globalData.unReadCount = commentCount
										}
										if (newMyCommentList.length != 0) {
											let newData = []
											newMyCommentList.forEach(el => {
												let newTime = that.getTime(el.ctime)
												el.ctime = newTime
												if (el.children) {
													el.children.forEach(el => {
														let childrenNewTime = that.getTime(el.ctime)
														el.ctime = childrenNewTime
													})
												}
												newData.push(el)
											})
											that.commentList = newData
											console.log(that.commentList, "that.commentList ")
											that.loadmoretype = 'end'
										} else {
											that.commentList = ""
											that.loadmoreShow = false
										}
									})
								} else if (item === '未读') {
									uniCloud.callFunction({
										name: 'getOtherCommentByOpenid',
										data: {
											openid: RES.data
										}
									}).then(res => {
										// 将两个数组中的对象进行合并
										let newMyCommentList
										// 拿出otherComment的read为false的评论
										let otherCommentList = []
										if (res.result.data.length != 0) {
											res.result.data[0].commentsList.forEach(el => {
												if (el.read === false) {
													otherCommentList.push(el)
												}
											})
											newMyCommentList = [...otherCommentList]
										}
										getApp().globalData.unReadCount = newMyCommentList.length
										if (newMyCommentList.length != 0) {
											// that.commentList = newMyCommentList
											let newData = []
											newMyCommentList.forEach(el => {
												let newTime = that.getTime(el.ctime)
												el.ctime = newTime
												if (el.children) {
													el.children.forEach(el => {
														let childrenNewTime = that.getTime(el.ctime)
														el.ctime = childrenNewTime
													})
												}
												newData.push(el)
											})
											that.commentList = newData
											console.log(that.commentList, "that.commentList ")
											that.loadmoretype = 'end'
										} else {
											that.commentList = ""
											that.loadmoreShow = false
										}
									})
								}
							}
						})
					}
				})
			},
			// 对获取到的数据进行时间的处理
			getTime(shijianchuo) {
				var date = new Date(shijianchuo); //时间戳为10位需*1000，时间戳为13位的话不需乘1000
				var Y = date.getFullYear() + '-';
				var M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
				var D = date.getDate() + ' ';
				var h = date.getHours() + ':';
				var m = date.getMinutes() < 10 ? '0' + date.getMinutes() + ':' : date.getMinutes() + ':';
				var s = date.getSeconds();
				return Y + M + D + h + m + s;
			},
			// 全部标为已读
			allRead() {
				const that = this
				getApp().globalData.unReadCount = 0
				uni.showLoading({
					title: '服务请求中...',
					mask: true
				})
				if (this.currentNav === '未读') {
					setTimeout(function() {
						that.commentList = null
						uni.hideLoading()
					}, 500)
				} else if (this.currentNav === "全部") {
					setTimeout(function() {
						that.commentList.forEach(el => {
							if (el.read === false) {
								el.read = true
							}
						})
						uni.hideLoading()
					}, 500)
				}
				// 拿到当前用户的openid，查找当前用户发布的所有商品，拿到商品的id
				uni.getStorage({
					key: 'openid',
					success(RES) {
						// 拿到用户的openid后
						uniCloud.callFunction({
							name: 'getUserPublishStoreByOpenid',
							data: {
								openid: RES.data
							}
						}).then((res) => {
							// 拿到了当前用户发布的所有商品
							console.log("拿到了当前用户发布的所有商品", res.result)
							if (res.result.data.length != 0) {
								res.result.data.forEach(function(v, i) {
									// 拿到当前用户的商品id，去请求questionTable
									uniCloud.callFunction({
										name: 'getQuestionTable',
										data: {
											store_id: v._id
										}
									}).then(res => {
										// 判断所有的商品是不是都有评论，有评论的拿出来
										if (res.result.data.length != 0) {
											res.result.data.forEach(function(v, i) {
												if (v.clickOpenid != RES.data && v.read == false) { //判断评论表是不是自己评论的
													console.log("parent", v)
													uniCloud.callFunction({
														name: 'updateCommentRead',
														data: {
															questionId: v._id,
															isParent: v.isParent,
															read: true
														}
													})
												}
												//判断当前商品的评论中是不是有回复
												if (v.children.length != 0) {
													v.children.forEach(function(val, ind) {
														if (val.replyOpenID != RES.data && val.read == false) { //判断回复表是不是自己回复的
															console.log("children", val)
															uniCloud.callFunction({
																name: 'updateCommentRead',
																data: {
																	questionId: val.question_id,
																	isChildren: val.isChildren,
																	read: true,
																	_id: val._id
																}
															}).catch(res => {
																console.log("出现异常", res)
															})
														}
													})
												}
											})
										}
									})
								})
							}
						})
						uniCloud.callFunction({
							name: 'getOtherCommentByOpenid',
							data: {
								openid: RES.data
							}
						}).then(res => {
							console.log("cccccccccccccccccccccccc", res.result.data[0].commentsList)
							if (res.result.data[0].commentsList.length != 0) {
								res.result.data[0].commentsList.forEach(el => {
									console.log("el", el)
									if (el.read === false) {
										uniCloud.callFunction({
											name: 'updateCommentRead',
											data: {
												openid: RES.data,
												_id: el._id,
												read: true
											}
										}).then(res => {
											console.log("getOtherCommentByOpenid修改成功", res)
										}).catch(res => {
											console.log("出现异常", res)
										})
									}
								})
							}
						})
					}
				})
			}
		},
		created() {
			const that = this
			uni.$on('myCommentList', data => {
				if (!data) {
					// 判断是否有评论
					this.loadmoreShow = false
				} else {
					let newData = []
					data.forEach(el => {
						console.log(el.ctime)
						let newTime = that.getTime(el.ctime)
						el.ctime = newTime
						if (el.children) {
							el.children.forEach(el => {
								let childrenNewTime = that.getTime(el.ctime)
								el.ctime = childrenNewTime
							})
						}
						newData.push(el)
					})
					this.commentList = newData
					console.log("this.commentList", this.commentList)
					this.loadmoretype = 'end'
				}

			})
		},
		onUnload() {
			uni.$off('myCommentList')
		}
	}
</script>

<style lang="scss">
	.cu-list.menu-avatar>.cu-item .action {
		width: 190rpx;
		text-align: right;
	}

	.cu-list.menu-avatar>.cu-item .content {
		width: 300rpx;
	}
</style>
