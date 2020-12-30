<template>
	<view class="mycontainer">
		<view class="cu-form-group margin-top">
			<view class="title">我的个人信息</view>
			<view class="text-lg text-orange cuIcon-roundclose text-right" @click="hideMask"></view>
		</view>
		<view class="cu-form-group">
			<view class="title">校区：</view>
			<text>{{userInfo.campus}}</text>
		</view>
		<view class="cu-form-group">
			<view class="title">系别：</view>
			<text>{{userInfo.department}}</text>
		</view>
		<view class="cu-form-group">
			<view class="title">班级：</view>
			<text>{{userInfo.class}}</text>
		</view>
		<view class="cu-form-group">
			<view class="title">学号：</view>
			<text>{{userInfo.sno}}</text>
		</view>
		<view class="cu-form-group">
			<view class="title">姓名：</view>
			<text>{{userInfo.name}}</text>
		</view>
		<view class="cu-form-group">
			<view class="title">手机：</view>
			<text>{{userInfo.phone}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		props:['userInfo'],
		data() {
			return {

			};
		},
		methods:{
			hideMask(){
				uni.$emit('hideMask',false)
			}
		}
	}
</script>

<style lang="scss">
	.mycontainer {
		width: 600rpx;
		.cu-form-group {
			min-height: 90rpx;
			justify-content: flex-start;
		}

		.cu-form-group+.cu-form-group {
			border-bottom: 1rpx solid #eee;
		}

		.cu-form-group:nth-of-type(2) {
			border-top: none;
		}

		.cu-form-group:nth-of-type(1) {
			border: none;
			.title {
				color: #000;
				margin: 0 auto;
				font-size: 35rpx;
			}
		}
		[class*="cuIcon-"] {
		    font-size: 45rpx;
		}
	}
</style>
