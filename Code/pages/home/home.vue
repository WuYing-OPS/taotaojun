<template>
	<view style="margin-bottom: 150rpx !important;">
		<view class="cu-custom ">
			<cu-custom :isBack="false" bgColor="bg-gradual-orange">
				<block slot="content">首页</block>
			</cu-custom>
		</view>
		<!-- 搜索 -->
		<view class="cu-bar bg-white search">
			<view class="action">
				<text class="cuIcon-locationfill text-orange"></text>
				<text>广州工商学院</text>
			</view>
			<view class="search-form round">
				<text class="cuIcon-search"></text>
				<input type="text" v-model="inputValue" @blur="blur(inputValue)" placeholder="搜索您想要的商品" confirm-type="search"
				 @confirm="toSearch"></input>
			</view>
		</view>
		<!-- 轮播图 -->
		<swiper class="card-swiper  square-dot" indicator-dots="true" circular="true" autoplay="true" interval="5000"
		 duration="500" indicator-color="#8799a3" indicator-active-color="#0081ff">
			<swiper-item v-for="(item,index) in swiperList" :key="index">
				<view class="swiper-item shadow">
					<image :src="item" mode="aspectFill"></image>
				</view>
			</swiper-item>
		</swiper>
		<!-- nav -->
		<scroll-view scroll-x class="bg-gradual-orange nav text-center">
			<view class="cu-item" v-for="(item,index) in navList" :key="item._id" :class="index==TabCur?'text-white cur':''"
			 @tap="tabSelect($event,navList[index].text)" :data-id="index">
				{{item.text}}
			</view>
		</scroll-view>
		<!-- item -->
		<view style="height: auto;margin-bottom: 150rpx;">
			<view class='card-menu container margin-top' v-for="(item,index) in storeList" :key="index">
				<navigator :url="'../detail/detail?data='+encodeURIComponent(JSON.stringify(item))" hover-class='navigator-hover'>
					<view class='container_img'>
						<image :src='item.imgList[0]'></image>
					</view>
					<view class='container_text'>
						<text class=''>{{item.title}}</text>
					</view>
					<view class='container_price text-gray text-sm'>
						<text class='container_price_text_0 text-orange'>￥{{item.price}}</text>
						<text class="cuIcon-attention text-orange">{{item.views}}</text>
						<text class="cuIcon-message text-orange">{{item.commentsCount}}</text>
					</view>
					<view class='container_line'></view>
					<view class='container_user'>
						<image :src='item.avatarUrl'></image>
						<text>{{item.nickName}}</text>
					</view>
				</navigator>
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
				myUnReadComment: 0, //我的未读信息
				showTop: true,
				show: false,
				userMessage: '',
				skip: 1,
				navSelected: '全部',
				pullDownLimit: 6,
				commentsCount: 0,
				storeId: '',
				countChange: false, //判断commentsCount是否已经改变
				loadmoreShow: true,
				loadmoretype: 'loading', //end 我是有底线的
				TabCur: 0,
				scrollLeft: 0,
				inputValue: '', //搜索框输入的内容
				swiperList: [
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/14882c30-2680-11eb-bd01-97bc1429a9ff.jpg',
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/13f031f0-2680-11eb-899d-733ae62bed2f.jpg',
					'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/12e3a3f0-2680-11eb-bd01-97bc1429a9ff.jpg'
				], //轮播图资源
				navList: [{
						id: 0,
						text: '全部',
						img: '&#xe7c2;'
					}, {
						id: 1,
						text: '生活用品',
						img: '&#xe7c2;'
					},
					{
						id: 2,
						text: '体育用品',
						img: '&#xe752;'
					},
					{
						id: 3,
						text: '学习用品',
						img: '&#xe613;'
					},
					{
						id: 4,
						text: '电子数码',
						img: '&#xe60d;'
					},
					{
						id: 5,
						text: '服饰',
						img: '&#xe600;'
					},
					{
						id: 6,
						text: '其他',
						img: '&#xe621;'
					},
				],
				storeList: '', //所有商品列表
			};
		},
		methods: {
			// 输入框失去焦点触发事件
			blur(inputVal) {
				let val = inputVal.replace(/(^\s*)|(\s*$)/g, "").toString()
				this.inputValue = val
				// uni.$emit("blur", val)
			},
			// 点击键盘搜索按钮触发事件
			toSearch(inputVal) {
				let toSearchVal = inputVal.detail.value.replace(/(^\s*)|(\s*$)/g, "").toString()
				// console.log(toSearchVal)
				uni.navigateTo({
					url: '../search/search?data=' + toSearchVal
				})
			},
			// 选择nav按钮触发事件
			tabSelect(e, data) {
				const that = this
				that.storeList = ''
				that.loadmoretype='loading'
				// data是nav点击的选项的值，this.inputValue是输入框的值
				let val = this.inputValue.replace(/(^\s*)|(\s*$)/g, "").toString()
				that.navSelected = data
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
				uniCloud.callFunction({
					name: "getAllStoreInHomePage",
					data: {
						"skip": 0,
						"pullDownLimit": that.pullDownLimit,
						"status": '已上架',
						"inVal": val,
						"navSelected": data,
					},
					success(res) {
						// console.log("res", res.result.data)
						that.skip = 1
						that.storeList = res.result.data
						if (res.result.data.length < 6) {
							that.loadmoretype = "end"
						} else {
							that.loadmoretype = "loading"
						}
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
		// 首次进入页面之前渲染数据
		beforeCreate() {
			const that = this
			uniCloud.callFunction({
				name: "getAllStoreInHomePage",
				data: {
					"skip": 0,
					"pullDownLimit": 6,
					"status": '已上架',
					"inVal": '',
					"navSelected": "全部",
				},
				success(res) {
					if(res.result.data.length===0){
						that.loadmoreShow=false
					}else if(res.result.data.length>0 && res.result.data.length<6){
						that.loadmoretype='end'
					}
					that.storeList = res.result.data
				}
			})
		},
		// 实现上拉加载
		onReachBottom() {
			const that = this;
			let oldData = []
			// console.log(that.navSelected)
			uniCloud.callFunction({
				name: 'getAllStoreInHomePage',
				data: {
					"skip": that.skip,
					"pullDownLimit": that.pullDownLimit,
					"status": '已上架',
					"inVal": that.inputValue,
					"navSelected": that.navSelected,
				}
			}).then(res => {
				const that = this
				if (res.result.data.length == 0) {
					that.loadmoretype = "end"
					// console.log("that.skip", that.skip)
					oldData = []
					return;
				} else {
					let oldData = that.storeList
					let newData = res.result.data
					let commitData = [...oldData, ...newData]
					that.storeList = commitData
					that.skip += 1
					if (that.loadmoretype == "end") {
						that.skip = 1
					}
				}
			})
		},
		// 实现下拉刷新
		onPullDownRefresh() {
			const that = this;
			that.loadmoretype = 'loading'
			that.skip = 0
			that.storeList = []
			uniCloud.callFunction({
				name: 'getAllStoreInHomePage',
				data: {
					"skip": that.skip,
					"pullDownLimit": that.pullDownLimit,
					"status": '已上架',
					"inVal": that.inputValue,
					"navSelected": that.navSelected,
				},
				success(res) {
					uni.stopPullDownRefresh()
					if(res.result.data.length===0){
						that.loadmoreShow=false
					}else if(res.result.data.length>0 && res.result.data.length<6){
						that.loadmoreShow=true
						that.loadmoretype='end'
					}
					that.storeList = res.result.data
					that.skip = 1
				}
			})
		},
		async onShow() {
			const that = this
			this.myUnReadComment=0
			console.log("this.storeList", this.storeList)
			if (this.countChange) {
				this.storeList.forEach((el) => {
					if (el._id == this.storeId) {
						el.commentsCount = this.commentsCount
					}
				})
			}
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
												that.myUnReadComment += 1
											}
											//判断当前商品的评论中是不是有回复
											if (v.children.length != 0) {
												v.children.forEach(function(val, ind) {
													// console.log(val.replyOpenID,RES.data)
													if (val.replyOpenID != RES.data && val.read === false) { //判断回复表是不是自己回复的
														that.myUnReadComment += 1
													}
												})
											}
										})
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
															that.myUnReadComment += 1
														}
													})
												}
											}
											// 更改我的person页面中的我的消息中的显示多少条未读的数量的
										getApp().globalData.unReadCount = that.myUnReadComment
											if (that.myUnReadComment > 0) {
												uni.showTabBarRedDot({
													index: 2
												})
											}
										})
									}
								})
							})
						}
						else{
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
												that.myUnReadComment += 1
											}
										})
									}
								}
								// 更改我的person页面中的我的消息中的显示多少条未读的数量的
							getApp().globalData.unReadCount = that.myUnReadComment
								if (that.myUnReadComment > 0) {
									uni.showTabBarRedDot({
										index: 2
									})
								}
							})
						}
					})

				}
			})
		},
		 created() {
			uni.$on('changeComemntCount', data => {
				this.countChange = true
				this.commentsCount = data.Count
				this.storeId = data.storeId
			})
			uni.$on('changeViews', data => {
				console.log("gg",data)
				this.storeList.forEach((el) => {
					if (el._id == data.storeId) {
						el.views = data.views
					}
				})
			})
		
		},
		onUnload() {
			uni.$off('changeComemntCount')
			uni.$off('changeViews')
		}
	}
</script>

<style lang="scss">
	.card-swiper swiper-item {
		padding: 0rpx 0rpx 60rpx;
	}

	.goTop {
		height: 60rpx;
		width: 60rpx;
		border-radius: 100%;
		position: fixed;
		bottom: 150rpx;
		right: 60rpx;
		z-index: 10000;

		image {
			width: 100%;
			height: 100%;
		}
	}

	/* 内容 */
	.container {
		margin-left: 29rpx;
		margin-right: 20rpx;
		float: left;
		// height: 530rpx;
		width: 43%;
		background: white;
	}

	.container_img image {
		height: 300rpx;
		width: 100%;
	}

	.container_text {
		color: black;
		padding: 10rpx;
		font-size: 23rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.container_price {
		display: flex;
		justify-content: space-between;
		padding-left: 8rpx;
		padding-right: 8rpx;
	}

	.container_price_text_0 {
		// color: red;
		font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
	}

	.container_price_text_1 {
		font-size: 22rpx;
	}

	.container_line {
		width: 100%;
		background: gainsboro;
		height: 1rpx;
		margin-top: 10rpx;
	}

	.container_user {
		margin-top: 20rpx;
		display: flex;
		line-height: 50rpx;

		text {
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;
		}
	}

	.container_user image {
		margin-left: 10rpx;
		margin-right: 50rpx;
		height: 50rpx;
		width: 50rpx;
		flex: 0 0 auto;
		border-radius: 50%;
	}
</style>
