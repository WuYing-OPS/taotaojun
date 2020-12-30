<template>
	<view>
		<view>
			<view class="cu-custom">
				<cu-custom :isBack="true" bgColor="bg-gradual-orange">
					<block slot="content">搜索</block>
					<block slot="backText">返回</block>
				</cu-custom>
			</view>
			<!-- 搜索框 -->
			<view class="cu-bar search">
				<view class="search-form round">
					<text class="cuIcon-search"></text>
					<input :adjust-position="false" type="text" placeholder="搜索您感兴趣的商品" confirm-type="search" v-model="inputVal"
					 @confirm="search"></input>
				</view>
				<view class="action">
					<button class="cu-btn bg-orange shadow-blur round" @click="search">搜索</button>
				</view>
			</view>
		</view>

		<!-- 导航条部分 -->
		<view class="cu-bar bg-white solid-bottom" style="z-index: 100000;">
			<scroll-view scroll-x class="bg-white nav">
				<view class="flex text-center">
					<view class="cu-item flex-sub" :class="index==TabCur?'text-orange cur':''" v-for="(item,index) in navList" :key="item.id"
					 @tap="tabSelect($event,navList[index].text)" :data-id="index">
						{{item.text}}
					</view>
				</view>
			</scroll-view>
		</view>

		<!-- item -->
		<view class="cu-card dynamic">
			<view class="cu-item shadow" v-for="(item,index) in storeList">
				<navigator :url="'../detail/detail?data='+encodeURIComponent(JSON.stringify(item))" hover-class='navigator-hover'>
					<view class="cu-list menu-avatar">
						<view class="cu-item">
							<view class="cu-avatar round lg" :style="{backgroundImage:`url(${item.avatarUrl})`}"></view>
							<view class="content flex-sub">
								<view class="text-grey">{{item.nickName}}</view>
								<view class="text-gray text-sm flex justify-between">
									{{item.ctime}}
									<view class="text-gray text-sm">
										<text class="cuIcon-recharge margin-lr-xs text-orange"></text> {{item.price}}
										<text class="cuIcon-attention margin-lr-xs text-orange"></text> {{item.views}}
										<text class="cuIcon-message margin-lr-xs text-orange"></text> {{item.commentsCount}}
									</view>
								</view>
							</view>
						</view>
					</view>
					<view class="text-content">
						{{item.title}}
					</view>
					<view class="grid flex-sub padding-lr col-1">
						<view class="bg-img only-img" :style="{backgroundImage:`url(${item.imgList[0]})`}">
						</view>
					</view>
				</navigator>
			</view>
		</view>
		<view class='goTop' @click="goTop">
			<image src='https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/580b4200-2728-11eb-b680-7980c8a877b8.png'
			 v-if="!showTop"></image>
		</view>
		<l-loadmore :show="true" :type="loadmoretype" loading-text="努力加载中~" />
	</view>
</template>

<script>
	export default {
		data() {
			return {
				countChange:false,//判断commentsCount是否已经改变
				commentsCount:0,
				showTop: true,
				TabCur: 0,
				scrollLeft: 0,
				inputVal: "",
				pullDownLimit: 3,
				navSelected: '按时间',
				skip: 1,
				loadmoretype: 'loading',
				storeList: '',
				navList: [{
						id: 0,
						text: '按时间'
					},
					{
						id: 1,
						text: '按流量'
					},
					{
						id: 2,
						text: '按评论数'
					},
					{
						id: 3,
						text: '按价格 ^'
					}
				]
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
			IsCard(e) {
				this.isCard = e.detail.value
			},
			tabSelect(e, data) {
				const that = this
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
				this.navSelected = data
				console.log(this.inputVal, this.navSelected)
				uniCloud.callFunction({
					name: "getUserSearchPublishMessage",
					data: {
						"skip": 0,
						"limit": that.pullDownLimit,
						"status": '已上架',
						"searchInput": that.inputVal,
						"navSelected": that.navSelected,
					},
					success(res) {
						console.log("res", res.result.data)
						that.skip = 1
						that.handleTime(res.result.data)
						// that.storeList = res.result.data
						if (res.result.data.length < 6) {
							that.loadmoretype = "end"
						} else {
							that.loadmoretype = "loading"
						}
					}
				})
			},
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
					// console.log(context[i].ctime)
					let newTime = getTime(context[i].ctime)
					context[i].ctime = newTime
				}
				this.storeList = context
			},
			// 搜索
			search(e) {
				const that = this
				that.loadmoretype = "loading"
				console.log(this.inputVal, this.navSelected)
				uniCloud.callFunction({
					name: "getUserSearchPublishMessage",
					data: {
						"skip": 0,
						"limit": that.pullDownLimit,
						"status": '已上架',
						"searchInput": that.inputVal,
						"navSelected": that.navSelected,
					},
					success(res) {
						console.log("res", res.result.data)
						that.skip = 1
						that.handleTime(res.result.data)
						// that.storeList = res.result.data
						if (res.result.data.length < 6) {
							that.loadmoretype = "end"
						} else {
							that.loadmoretype = "loading"
						}
					}
				})
			},
			// 实现上拉加载
			onReachBottom() {
				const that = this;
				let oldData = []
				that.loadmoretype = "loading"
				// console.log(that.navSelected)
				uniCloud.callFunction({
					name: 'getUserSearchPublishMessage',
					data: {
						"skip": that.skip,
						"limit": that.pullDownLimit,
						"status": '已上架',
						"searchInput": that.inputVal,
						"navSelected": that.navSelected,
					}
				}).then(res => {
					const that = this
					console.log(res)
					if (res.result.data.length == 0) {
						that.loadmoretype = "end"
						console.log("that.skip", that.skip)
						oldData = []
						return;
					} else {
						let oldData = that.storeList
						console.log("olddata", oldData)
						let newData = res.result.data
						console.log("newData", newData)
						let commitData = [...oldData, ...newData]
						console.log("commitData", commitData)
						// that.storeList = commitData
						that.handleTime(commitData)
						that.skip += 1
						console.log("that.skip", that.skip)
						if (that.loadmoretype == "end") {
							that.skip = 1
							console.log(that.skip)
						}
					}
				})
			},
		},
		onLoad(options) {
			const that = this
			this.inputVal = options.data
			uniCloud.callFunction({
				name: "getUserSearchPublishMessage",
				data: {
					"skip": 0,
					"limit": that.pullDownLimit,
					"status": '已上架',
					"searchInput": that.inputVal,
					"navSelected": that.navSelected,
				},
				success(res) {
					console.log("res", res.result.data)
					// that.skip = 1
					// that.storeList = res.result.data
					that.handleTime(res.result.data)
					if (res.result.data.length < 6) {
						that.loadmoretype = "end"
					} else {
						that.loadmoretype = "loading"
					}
				}
			})
		},
		onShow() {
			console.log(this.storeList)
			if(this.countChange){
				this.storeList.forEach((el)=>{
					if(el._id==this.storeId){
						el.commentsCount = this.commentsCount
					}
				})
			}
		},
		created() {
			uni.$on('changeComemntCount',data=>{
				this.countChange=true
				this.commentsCount = data.Count
				this.storeId=data.storeId
			})
			uni.$on('changeViews',data=>{
				// console.log("views",data)
				this.storeList.forEach((el)=>{
					if(el._id==data.storeId){
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
	.bg-img {
		background-size: cover;
	}
	.cu-card>.cu-item {
		margin: 15rpx 30rpx;
	}
	.text-content {
		padding-left: 30rpx;
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
</style>
