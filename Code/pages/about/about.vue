<template>
	<view>
		<view class="cu-custom">
			<cu-custom bgColor="bg-gradual-orange" :isBack="true">
				<block slot="content">关于</block>
				<block slot="backText">返回</block>
			</cu-custom>
		</view>
		<view class="about">
			<view class="img">
				<image src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-xujv1rimzfwq8ea087/fe25e930-160f-11eb-899d-733ae62bed2f.jpg"></image>
			<!-- 	<view class="img_title">
					<text>Second Hand Hransaction</text>
				</view>
				<view class="img_icon">
					<view class="t-icon t-icon-chongwutubiao23 ">

					</view>
				</view> -->
			</view>
			<view class="title">
				<text>关于二货</text>
				<text class="bor"></text>
			</view>
			<view class="content">
				<text>欢迎来到二货小程序！</text>
				<text>"二货"是我个人开发的一款小程序，它是基于GGS校园闲置物品交易的一款小程序</text>
				<text>开发此项目的初衷是希望能把学生身边闲置的物品"易"交给下一位有需要的主人，这样同学们可以随时发布自己闲置的物品，也能入手自己想要的商品</text>
				<text>所以，“二货”小程序诞生了！</text>
				<text>更多功能，敬请期待!</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			};
		}
	}
</script>

<style lang="scss">
	.about {
		.img {
			position: relative;

			image {
				width: 750rpx;
			}

			.img_title {
				position: absolute;
				color: #fff;
				left: 0;
				bottom: 12rpx;
				width: 750rpx;
				box-sizing: border-box;
				height: 60rpx;
				line-height: 60rpx;
				background-color: rgba(0, 0, 0, 0.5);
				padding-left: 195rpx;
				font-weight: 550;
			}

			// .img_icon {
			// 	position: absolute;
			// 	left: 45rpx;
			// 	bottom: -26rpx;
			// 	.t-icon {
			// 		width: 110rpx;
			// 		height: 110rpx;
			// 		z-index: 999;
			// 		background-color: #fff;
			// 		border-radius: 50%;
			// 		box-shadow: 0 0 12px #FFFFFF;
			// 	}
			// }
		}

		.title {
			display: flex;
			flex-direction: column;
			padding: 45rpx;
			color: #1e1e1e;
			font-weight: bold;
			font-size: 40rpx;

			.bor {
				margin-top: 25rpx;
				width: 80rpx;
				border-bottom: 2px solid #1e1e1e;
			}
		}

		.content {
			padding: 0rpx 45rpx 45rpx;
			display: flex;
			flex-direction: column;
			color: #a2a2a2;

			text {
				margin: 10rpx 0;
				line-height: 50rpx;
			}

		}
	}
</style>
