<template>
	<view style="margin-bottom: 150rpx !important;">
		<view class="cu-custom bg-gradual-orange">
			<cu-custom :isBack="false">
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
				<!-- 	<text v-html="item.img"></text> -->{{item.text}}
			</view>
		</scroll-view>

		<!-- item -->
		<view style="height: auto;margin-bottom: 150rpx;">
			<view class='card-menu container margin-top' v-for="(item,index) in storeList" :key="index">
				<navigator url='/pages/home/home_detail/home_detail' hover-class='none'>
					<view class='container_img'>
						<image :src='item.imgList[0]'></image>
					</view>
					<view class='container_text'>
						<text class=''>{{item.title}}</text>
					</view>
					<view class='container_price'>
						<text class='container_price_text_0'>￥{{item.price}}</text>
						<text class="cuIcon-attentionfill text-gray text-sm">{{item.views}}</text>
						<text class="cuIcon-messagefill text-gray text-sm">{{item.commentsCount}}</text>
					</view>
					<view class='container_line'></view>
					<view class='container_user'>
						<image :src='item.avatarUrl'></image>
						<text>{{item.nickName}}</text>
					</view>


				</navigator>
			</view>
		</view>
		<l-loadmore :show="loadmoreShow" :type="loadmoretype" loading-text="努力加载中~" />

	</view>
</template>

<script>
	export default {
		data() {
			return {
				loadmoreShow:true,
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
				uni.$emit("blur", val)
			},
			// 点击键盘搜索按钮触发事件
			toSearch(inputVal) {
				let toSearchVal = inputVal.detail.value.replace(/(^\s*)|(\s*$)/g, "").toString()
				// console.log(toSearchVal)
			},
			// 选择nav按钮触发事件
			tabSelect(e, data) {
				uni.$emit("tabSelect", data)
				let val = this.inputValue.replace(/(^\s*)|(\s*$)/g, "").toString()
				// uni.$emit("blur",val)
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
				const that = this
				uniCloud.callFunction({
					name: "getAllStoreInHomePage",
					data: {
						"skip": 0,
						"pullDownLimit": 6,
						"status": '已上架',
						"inVal": val,
						"navSelected": data,
					},
					success(res) {
						console.log("res", res.result.data)
						uni.$emit('changeUsersPublishMessage', res.result.data)
						uni.$emit('storeList', res.result.data)
						if (res.result.data.length != 0) {
							uni.$emit('changeLoadmoretype', 'loading')
						} else {
							uni.$emit('changeLoadmoretype', 'end')
						}
					}
				})
			}

		},
		created() {
			console.log("beforecreate")
			uni.$on('storeList', data => {
				this.storeList = data
				// console.log("this.storeList", this.storeList)
			})
			uni.$on('storeItem', data => {
					this.storeList = data
					console.log("this.storeList", this.storeList)
				})
			uni.$on('changeLoadmoretype', data => {
				this.loadmoretype = data
			})
			uni.$on('changeLoadmoreShow', data => {
				this.loadmoreShow = data
				console.log("this.loadmoreShow",this.loadmoreShow)
			})
		},
		onUnload() {
			// uni.$off('storeItem')
			uni.$off('storeList')
			uni.$off('changeLoadmoretype')
			uni.$off('changeLoadmoreShow')
			uni.$off('tabSelect')
			uni.$off('blur')
		}
	}
</script>

<style lang="scss">
	.card-swiper swiper-item {
		padding: 0rpx 0rpx 60rpx;
	}

	// .nav-select {
	// 	width: 720rpx;
	// 	display: flex;
	// 	font-size: 26rpx;
	// 	background-color: #FFFFFF;
	// 	flex-wrap: wrap;
	// 	justify-content: space-around;
	// 	align-items: center;
	// 	margin: 0 auto;
	// 	box-sizing: border-box;
	// 	border-radius: 5rpx;
	// 	padding: 15rpx 0rpx;

	// 	.nav-item {
	// 		width: 200rpx;
	// 		box-sizing: border-box;
	// 		padding: 30rpx;
	// 		text-align: center;

	// 		.nav-icon {
	// 			font-size: 40rpx;
	// 		}
	// 	}

	// 	.nav-item:nth-of-type(1) .nav-icon {
	// 		color: #ff6b31;
	// 	}

	// 	.nav-item:nth-of-type(2) .nav-icon {
	// 		color: #1e92eb;
	// 	}

	// 	.nav-item:nth-of-type(3) .nav-icon {
	// 		color: #ff9b0e;
	// 	}

	// 	.nav-item:nth-of-type(4) .nav-icon {
	// 		color: #67bc2f;
	// 	}

	// 	.nav-item:nth-of-type(5) .nav-icon {
	// 		color: #cc3d52;
	// 	}

	// 	.nav-item:nth-of-type(6) .nav-icon {
	// 		color: #191919;
	// 	}

	// }

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
		color: red;
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
