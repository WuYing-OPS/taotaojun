<template>
	<view>
		<view class="cu-custom ">
			<cu-custom :isBack="true" bgColor="bg-gradual-orange">
				<block slot="backText">返回</block>
				<block slot="content">详情</block>
			</cu-custom>
		</view>
		<!-- 头部 -->
		<view class="cu-list menu-avatar">
			<view class="cu-item">
				<view class="cu-avatar round lg" :style="{backgroundImage:`url(${detail.avatarUrl})`}"></view>
				<view class="content flex-sub">
					<view>{{detail.nickName}}</view>
					<view class="text-gray text-sm flex">
						<text class="margin-right-sm">{{detail.ctime}}</text>
						<text class="cuIcon-attentionfill margin-lr-sm"> {{detail.views}}</text>
						<text class="cuIcon-messagefill margin-lr-sm">{{commentsCount}}</text>
					</view>
				</view>
				<view class="cu-list grid" v-if="detail.openid!==clickOpenid && login" @click="saveStore(detail._id,detail.openid)">
					<view class="cu-item">
						<text class="lg text-orange" :class="isSave?'cuIcon-favorfill':'cuIcon-favor'"></text>
						<text>收藏</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 轮播 -->
		<!-- 轮播图 -->
		<view class="margin-top-sm">
			<swiper class="screen-swiper square-dot " :indicator-dots="true" :circular="true" :autoplay="false" interval="5000"
			 duration="500">
				<swiper-item v-for="(item,index) in detail.imgList" :key="index" @tap="handlePreviewImg" :data-url="item">
					<image :src="item" mode="aspectFill"></image>
				</swiper-item>
			</swiper>
			
		</view>
		<!-- 描述 -->
		<view class="cu-list  bg-white padding-bottom-xs">
			<view class="cu-item">
				<view class="content margin-lr-xs">
					<view class="text-orange text-lg ">￥{{detail.price}}</view>
					<view class="text-black text-lg ">
						{{detail.title}}
					</view>
					<view class="text-orange text-sm margin-top-sm">
						#{{detail.classify}}
					</view>
					<view class="text-abc text-sm ">
						{{detail.description}}
					</view>
				</view>
			</view>
		</view>
		<!-- 联系 -->
		<form>
			<view class="cu-form-group margin-top-sm">
				<view class="title">联系人</view>
				<input v-if="login" :value="detail.connection" name="input" disabled></input>
				<input v-else value="***" name="input" disabled></input>
			</view>
			<view class="cu-form-group">
				<view class="title">联系人手机</view>
				<input v-if="login" :value="detail.phone" name="input" disabled @click="makePhoneCall(detail.phone)"></input>
				<input v-else value="***" name="input" disabled></input>
			</view>
			<view class="cu-form-group special">
				<view class="title">联系微信</view>
				<text v-if="login" selectable :data-weixin="detail.weixin" @click="setClipboardData">{{detail.weixin}}</text>
				<input v-else value="***" name="input" disabled></input>
			</view>
		</form>
		<!-- 问答区 -->
		<view class="margin-bottom-xl padding-bottom-xl">
			<view class="comments-count margin-top-sm ">
				<text>问答区({{commentsCount}})</text>
			</view>
			<!-- 评论列表 -->
			<view class=" bg-white " style="overflow-x: hidden">
				<view class=" msg padding-sm" v-for="comments in comment" :key="comments._id" style="border-bottom: 1px solid #F1F1F1;">
					<view class="  msg padding-left-sm flex" @click="clickParent(`${comments.clickNickName}`,`${comments.ctime}`,`${comments.clickOpenid}`)">
						<view class=" padding-top-xs">
							<image :src="comments.clickAvatarUrl" mode="" class="cu-avatar round lg"></image>
						</view>
						<view class=" msg-conetent margin-left-sm">
							<view class=" flex justify-between padding-xs">
								<view class="nickname">
									{{comments.clickNickName}}
								</view>
								<view class=" msg-timers  margin-right-sm"> {{comments.newTime}}</view>

							</view>
							<view class="pinglun padding-right-xs">
								{{comments.commentValue}}
							</view>

						</view>
					</view>
					<!-- 回复表  -->
					<view class=" msg margin-left-xl padding-left-xl flex padding-bottom-xs padding-top-xs" v-for="childrenComment in comments.children"
					 :key="childrenComment._id" @click="clickChildren(`${childrenComment.question_id}`,`${childrenComment.replyNickName}`,`${childrenComment.replyOpenID}`)">
						<view class=" padding-top-xs">
							<image :src="childrenComment.replyAvatarUrl" mode="" class="cu-avatar round lg"></image>
						</view>
						<view class=" msg-conetent margin-left-sm">
							<view class=" flex justify-between padding-xs">
								<view class="nickname">
									{{childrenComment.replyNickName}}
								</view>
								<view class=" msg-timers "> {{childrenComment.newTime}}</view>

							</view>
							<view class="pinglun padding-right-sm">
								<text class="padding-right-sm text-orange">@{{ childrenComment.clickNickName }}</text>{{childrenComment.replyContent}}
							</view>

						</view>
					</view>
				</view>
			</view>

		</view>
		<!-- 输入操作条 -->
		<view class="box">
			<view class="cu-bar input" style="position: fixed;bottom: 0;width: 100%;">
				<view class="action">
					<text class="text-orange" v-if="!login" @click="confirmLogin">请登录</text>
					<text v-if="peopleClick">@{{ name }}</text>
				</view>
				<input :adjust-position="false" class="solid-bottom" maxlength="300" cursor-spacing="10" adjust-position v-model="commentValue"></input>
				<button class="cu-btn bg-green shadow-blur" @click="submitComment(peopleClick)">
					<text v-if="peopleClick">回复</text>
					<text v-else>评论</text>
				</button>
				<view class="action">
					<text class="cuIcon-roundclose text-grey" v-if="peopleClick" @click="cancelIcon"></text>
				</view>
			</view>
		</view>
		<l-message />
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
				showTop: true, //火箭
				login: false,
				peopleClick: false,
				InputBottom: 0,
				name: '',
				commentsCount: 0, //评论数
				views: 0, //浏览数
				isSave: false,
				swiperList: [
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/14882c30-2680-11eb-bd01-97bc1429a9ff.jpg',
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/13f031f0-2680-11eb-899d-733ae62bed2f.jpg',
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/12e3a3f0-2680-11eb-bd01-97bc1429a9ff.jpg'
				],
				otherOpenid: '',
				comment: '', //评论列表
				detail: '', //用户传递过来的详情信息
				storeID: '',
				storeTitle: '',
				storeOpenID: '',
				clickOpenid: '',
				userInfo: '',
				commentValue: '',
				name: '',
				questionID: '',
				clickName: '',
				pullDownLimit: 5
			};
		},
		methods: {
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
			// 轮播图预览图片
			handlePreviewImg(e){
				console.log(e)
				uni.previewImage({
					current: e.currentTarget.dataset.url,
					urls:this.detail.imgList,
				});
			},
			// 点击长按复制
			setClipboardData(e){
				uni.setClipboardData({
				    data: e.currentTarget.dataset.weixin
				});
			},
			// 点击收藏商品
			async saveStore(storeid, storeOpenid) {
				const that = this
				let timer
				clearTimeout(timer)
				that.isSave = !that.isSave
				if (that.isSave) {
					timer = await setTimeout(function() {
						uniCloud.callFunction({
							name: 'addStoreSave',
							data: {
								storeId: storeid,
								storeOpendId: storeOpenid,
								userOpenid: that.clickOpenid,
								save: true
							}
						}).then(res => {
							console.log("收藏成功", res)
							// 得到用户收藏商品的总数哦
							uniCloud.callFunction({
								name: 'getAllStoreSave',
								data: {
									openid: that.clickOpenid
								},
							}).then(res => {
								uni.$emit('changeSaveCount', res.result.data.length)
							})
						})
					}, 1000)
				} else {
					timer = await setTimeout(function() {
						// console.log("取消收藏")
						uniCloud.callFunction({
							name: 'deleteStoreSave',
							data: {
								storeId: storeid,
								userOpenid: that.clickOpenid
							}
						}).then(res => {
							uniCloud.callFunction({
								name: 'getAllStoreSave',
								data: {
									openid: that.clickOpenid
								}
							}).then(res => {
								let store = []
								console.log("收藏的商品", res.result.data)
								if (res.result.data.length === 0) {
									uni.$emit('getAllSave', "")
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
											// that.handleTime(store)
											uni.$emit('getAllSave', store)

										}
									})
								})
							})
						})
					}, 1000)
				}
				// }
			},
			// 拨打电话撒
			makePhoneCall(phone) {
				uni.showModal({
					content: '确定拨打电话吗？',
					success(res) {
						if (res.confirm) {
							uni.makePhoneCall({
								phoneNumber: phone //仅为示例
							});
						}
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
			handleTime(context) {
				let newTime = this.getTime(context.ctime)
				context.ctime = newTime
				this.detail = context
				this.commentsCount = this.detail.commentsCount
				console.log("detail", this.detail)
			},
			// 对信息的提示
			wxShowMessage(type, icon, content) {
				wx.lin.showMessage({
					type: type,
					icon: icon,
					content: content
				})
			},
			// 点击请登录跳转登录页面
			confirmLogin() {
				uni.showModal({
					title: '温馨提示',
					content: '确定登陆吗？',
					success: function(res) {
						if (res.confirm) {
							uni.navigateTo({
								url: '../login/login'
							})
						}
					}
				})
			},
			// 评论
			discuss() {
				const that = this
				console.log("评论")
				uni.showLoading({
					title: '评论中...',
					mask: true
				});
				uniCloud.callFunction({
					name: 'addQuestionTable',
					data: {
						storeID: that.storeID,
						storeTitle: that.storeTitle,
						storeOpenID: that.storeOpenID,
						clickOpenid: that.clickOpenid,
						clickNickName: that.userInfo.nickName,
						clickAvatarUrl: that.userInfo.avatarUrl,
						commentValue: that.commentValue,
						isParent: true,
						read: false,
						children: [],
						ctime: new Date().getTime()
					},
					success(res) {
						uni.hideLoading()
						that.wxShowMessage("success", "success", "评论成功")
						that.commentsCount += 1
						that.commentValue = ''
						uni.$emit('changeComemntCount', {
							Count: that.commentsCount,
							storeId: that.storeID
						})
						// 更改商品列表的评论数量
						uniCloud.callFunction({
							name: 'updateCommentCount',
							data: {
								store_id: that.storeID,
								commentsCount: that.commentsCount
							},
							success(res) {
								console.log("更改CommentCount")
							}
						})
						// 重新渲染评论列表
						uniCloud.callFunction({
							name: 'getQuestionTable',
							data: {
								store_id: that.storeID,
							},
							success(res) {
								// 对时间进行处理
								let newData = []
								res.result.data.forEach((el) => {
									let newTime = that.getTime(el.ctime)
									el.newTime = newTime
									if (el.children) {
										el.children.forEach(el => {
											let childrenNewTime = that.getTime(el.ctime)
											el.newTime = childrenNewTime
										})
									}
									newData.push(el)
								})
								that.comment = newData
							}
						})
					}
				})
			},
			// 回复
			reply() {
				const that = this
				console.log("回复", that.otherOpenid, that.commentValue)
				uni.showLoading({
					title: '回复中...',
					mask: true
				});
				uniCloud.callFunction({
					name: 'addReplyTable',
					data: {
						_id: Math.floor(Math.random() * 10 + 10000000),
						store_id: that.storeID,
						storeTitle: that.storeTitle,
						read: false,
						isChildren: true,
						question_id: that.questionID,
						storeOpenID: that.storeOpenID,
						replyOpenID: that.clickOpenid,
						replyContent: that.commentValue,
						replyNickName: that.userInfo.nickName,
						replyAvatarUrl: that.userInfo.avatarUrl,
						clickNickName: that.clickName,
						ctime: new Date().getTime()
					},
					success(res) {
						if (that.storeOpenID === that.otherOpenid){
							uni.hideLoading()
							that.wxShowMessage("success", "success", "回复成功")
							that.commentValue = ''
						}
						that.commentsCount += 1
						uni.$emit('changeComemntCount', {
							Count: that.commentsCount,
							storeId: that.storeID
						})
						// 更改商品列表的评论数量
						uniCloud.callFunction({
							name: 'updateCommentCount',
							data: {
								store_id: that.storeID,
								commentsCount: that.commentsCount
							},
							success(res) {
								console.log("更改CommentCount")
							}
						})
						// 重新渲染评论列表
						uniCloud.callFunction({
							name: 'getQuestionTable',
							data: {
								store_id: that.storeID,
							},
							success(res) {
								// 对时间进行处理
								let newData = []
								res.result.data.forEach((el) => {
									let newTime = that.getTime(el.ctime)
									el.newTime = newTime
									if (el.children) {
										el.children.forEach(el => {
											let childrenNewTime = that.getTime(el.ctime)
											el.newTime = childrenNewTime
										})
									}
									newData.push(el)
								})
								that.comment = newData
							}
						})

					}
				})
				if (that.storeOpenID !== that.otherOpenid) {
					console.log("不相同", that.storeOpenID, that.clickOpenid, that.commentValue)
					uniCloud.callFunction({
						name: 'getOtherCommentByOpenid',
						data: {
							openid: that.otherOpenid
						}
					}).then(res => {
						console.log("otherOpenid", res, that.commentValue, res.result.data.length)
						if (res.result.data.length != 0) {
							console.log("that.commentValue", that.commentValue, that.storeID)
							// 更新otherComment
							uniCloud.callFunction({
								name: 'updateOtherCommentByOPenid',
								data: {
									_id:Math.floor(Math.random() * 10 + 10000000),
									openid: that.otherOpenid,
									storeId: that.storeID,
									comment: that.commentValue,
									replyNickName: that.userInfo.nickName,
									replyAvatarUrl: that.userInfo.avatarUrl,
									ctime: new Date().getTime(),
									read:false
								}
							}).then(res => {
								console.log("修改otherComment成功", res)
								uni.hideLoading()
								that.wxShowMessage("success", "success", "回复成功")
								that.commentValue = ''
								console.log(that.otherOpenid, that.storeID, that.commentValue, that.userInfo.nickName, that.userInfo.avatarUrl)
							})
						} else {
							uniCloud.callFunction({
								name: 'addOtherReplyTable',
								data: {
									_id:Math.floor(Math.random() * 10 + 10000000),
									openid: that.otherOpenid,
									storeID: that.storeID,
									comment: that.commentValue,
									replyNickName: that.userInfo.nickName,
									replyAvatarUrl: that.userInfo.avatarUrl,
									read:false,
									ctime: new Date().getTime()
								}
							}).then(res => {
								uni.hideLoading()
								that.wxShowMessage("success", "success", "回复成功")
								that.commentValue = ''
								console.log(res, "别人的评论成功")
							})
						}
					})

				}
			},
			// 评论 OR 回复
			submitComment(peopleClick) {
				const that = this;
				if (!this.login) {
					uni.showModal({
						title: "温馨提示",
						content: '点击前往登录页面',
						success(e) {
							if (e.confirm) {
								uni.navigateTo({
									url: '../login/login'
								})
							}
						}
					})
				} else if (this.login) {
					that.commentValue = that.commentValue.replace(/(^\s*)|(\s*$)/g, "")
					// 判断有没有输入内容
					if (!that.commentValue) {
						that.wxShowMessage("error", "error", "评论/回复的内容不能为空")
					} else {
						// 判断是评论还是回复
						if (peopleClick) {
							// 回复
							that.reply()
						} else {
							// 评论
							that.discuss()
						}
					}
				}
			},
			clickParent(Pname, ctime, clickOpenid) {
				console.log(Pname, ctime, clickOpenid)
				const that = this
				console.log()
				that.otherOpenid = clickOpenid
				console.log("clickParent", that.storeOpenID, that.clickOpenid)
				if (that.login && clickOpenid != that.clickOpenid) {
					console.log(ctime, clickOpenid)
					that.peopleClick = true
					that.name = Pname
					uniCloud.callFunction({
						name: 'getQuestionId',
						data: {
							ctime: Number(ctime),
							clickOpenid: clickOpenid
						},
						success(res) {
							that.questionID = res.result.data[0]._id
							that.clickName = Pname
						}
					})
				}
			},
			clickChildren(question_id, Cname, replyOpenID) {
				console.log(question_id, Cname, replyOpenID)
				const that = this
				that.otherOpenid = replyOpenID
				console.log("clickChildren", that.storeOpenID, that.clickOpenid)
				if (that.login && replyOpenID != that.clickOpenid) {
					that.peopleClick = true
					that.name = Cname
					that.questionID = question_id
					that.clickName = Cname
				}
			},
			cancelIcon() {
				this.name = ''
				this.peopleClick = false
			},
			// 获取该商品是否是收藏的
			judgeStoreSave(that) {
				uniCloud.callFunction({
					name: 'getStoreSave',
					data: {
						storeID: that.storeID,
						userOpenid: that.clickOpenid
					}
				}).then(res => {
					if (res.result.data.length != 0) {
						that.isSave = true
					}
				})
			},
		},
		// 接收商品的个人信息
		onLoad: function(option) {
			console.log("onload")
			const that = this
			let data = JSON.parse(decodeURIComponent(option.data))
			console.log("data", data)
			this.storeID = data._id
			this.storeOpenID = data.openid
			this.storeTitle = data.title
			this.views = data.views + 1
			this.handleTime(data)
			uni.$emit('changeViews', {
				views: that.views,
				storeId: that.storeID
			})
			uniCloud.callFunction({
				name: 'updateViews',
				data: {
					store_id: that.storeID,
					views: that.detail.views + 1
				}
			})
			// 获取评论列表
			uniCloud.callFunction({
				name: 'getQuestionTable',
				data: {
					store_id: that.storeID
				},
				success(res) {
					// 对时间进行处理   
					let newData = []
					res.result.data.forEach((el) => {
						let newTime = that.getTime(el.ctime)
						el.newTime = newTime
						if (el.children) {
							el.children.forEach(el => {
								let childrenNewTime = that.getTime(el.ctime)
								el.newTime = childrenNewTime
							})
						}
						newData.push(el)
					})
					that.comment = newData
				}
			})


		},
		// 判断有无openid，有就代表已经登录过了
		created() {
			const that = this
			uni.getStorage({
				key: 'openid',
				success(res) {
					that.clickOpenid = res.data
					that.login = true
					console.log("that.clickOpenid", that.clickOpenid)
					// 获取该商品是否是收藏的
					that.judgeStoreSave(that)
				}
			})
			uni.getUserInfo({
				success(res) {
					that.userInfo = res.userInfo
				}
			})
		},
		onUnload() {
			uni.$off("views")
			uni.$off("clickOpenid")
		},
		// 下拉刷新
		onPullDownRefresh() {
			const that = this
			// 获取该商品是否是收藏的
			that.judgeStoreSave(that)
			uniCloud.callFunction({
				name: 'getStoreDetail',
				data: {
					store_id: that.storeID
				}
			}).then(res => {
				uni.stopPullDownRefresh()
				this.handleTime(res.result.data[0])
				that.commentValue = res.result.data[0].commentValue
			})
		}

	}
</script>

<style lang="scss">
	.cu-list.grid>.cu-item [class*=cuIcon] {
		color: orange;
	}

	.cu-form-group {
		min-height: 80rpx;
	}

	.comments-count {
		background-color: #FFFFFF;
		padding: 19rpx;
		font-size: 32rpx;
		border-bottom: 1px solid #bcbcbc;

		text {
			color: #003200;
			border-left: 3px solid orange;
			padding-left: 30rpx;
			border-radius: 3rpx;
		}
	}


	.margin-bottom-xl {
		margin-bottom: 60rpx;
	}

	.margin-left-sm {
		width: 600rpx;
	}

	.nickname {
		width: 43%;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.pinglun {
		width: 100%;
		line-height: 32rpx;
	}

	.cu-custom .cu-bar {
		z-index: 500;
	}

	.cu-bar.fixed,
	.nav.fixed {
		z-index: 500;
	}

	.cu-form-group.special {
		justify-content: flex-start;
	}
</style>
