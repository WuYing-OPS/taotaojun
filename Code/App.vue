<script>
	export default {
		globalData:{
			userInfo:'',
			unReadCount:0
		},
		onLaunch: function() {
			console.log('App Launch')
		},
	  	onShow:  function() {
			const that = this
			console.log('App Show')
			uni.getStorage({
				key: 'openid',
				success(res) {
					console.log("openid", res.data)
					uniCloud.callFunction({
						name: 'getUserInfoByOpenid',
						data: {
							openid: res.data
						}
					}).then(res => {
						that.globalData.userInfo=res.result.data[0]
					})
				}
			})
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	@import "colorui/main.css";
	@import "colorui/icon.css";
	@font-face {
		font-family: 'iconfont';
		/* project id 2115797 */
		src: url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.eot');
		src: url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.eot?#iefix') format('embedded-opentype'),
			url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.woff2') format('woff2'),
			url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.woff') format('woff'),
			url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.ttf') format('truetype'),
			url('//at.alicdn.com/t/font_2115797_g6uk9cd75km.svg#iconfont') format('svg');
	}
	.iconfont {
		font-family: "iconfont" !important;
		font-size: 16px;
		font-style: normal;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
	}
	/*每个页面公共css */
	page {
		height: 100%;
		background-color: #f1f1f1;
	}

	// 头部
	.cu-custom {
		height: 121rpx;

		.bg-gradual-blue {
			height: 121rpx;
		}

		.cu-bar {
			height: 121rpx;
			padding-top: 41rpx;

			.content {
				top: 50rpx;
			}
		}
	}

	// tabbar

	.bar-wrapper {
		position: fixed;
		z-index: 100;
		left: 0;
		right: 0;
		bottom: 0;
	}
</style>
