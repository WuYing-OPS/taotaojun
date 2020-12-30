<template>
	<view>
		<scroll-view scroll-y class="scrollPage">
			<view class="UCenter-bg">
				<image :src="userMessage.avatarUrl" class="png round" mode="widthFix" @click="login"></image>
				<view class="text-xl margin-top text-black" @click="login">
					{{userMessage.name}}
				</view>
				<view class="margin-top-sm" @click="login">
					<text class="text-black">{{userMessage.class}}</text>
				</view>
				<image src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/52812180-24bf-11eb-9dfb-6da8e309e0d8.gif" mode="scaleToFill" class="gif-wave"></image>
			</view>
			<view class="padding flex text-center text-grey bg-white shadow-warp">
				<view class="flex flex-sub flex-direction solid-right" @click="onEnterHomeTransaction">
					<view class="text-xxl text-blue">0</view>
					<view class="margin-top-sm">
						总交易数</view>
				</view>
				<view class="flex flex-sub flex-direction" @click="onEnterHomeRelease">
					<view class="text-xxl text-green">0</view>
					<view class="margin-top-sm">
						总发布商品数</view>
				</view>
			</view>
			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg radius">
				<view class="cu-item arrow" @click="onAuthReceiveMsg">
					<view class="content" hover-class="none">
						<text class="cuIcon-forwardfill text-green"></text>
						<text class="text-grey">接受新交易推送一次</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeUserInfo">
					<view class="content" hover-class="none">
						<text class="cuIcon-peoplefill text-orange"></text>
						<text class="text-grey">我的信息</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeTransaction">
					<view class="content" hover-class="none">
						<text class="cuIcon-rechargefill text-red"></text>
						<text class="text-grey">我的交易</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="onEnterHomeRelease">
					<view class="content" hover-class="none">
						<text class="cuIcon-formfill text-blue"></text>
						<text class="text-grey">我发布的商品</text>
					</view>
				</view>
				<view class="cu-item arrow" @click="copyLink">
					<view class="content" data-link="https://github.com/2horse9sun/University_O2O">
						<text class="cuIcon-github text-grey"></text>
						<text class="text-grey">复制此小程序的GitHub地址</text>
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
						<text class="text-grey">关于此小程序</text>
					</view>
				</view>
		
			</view>
			<view class="cu-tabbar-height"></view>
		</scroll-view>
		
		
		<view class="hline"></view>
			<!--确认框 -->
			<l-dialog 
			  :show="show"
			  ></l-dialog>
	</view>
</template>

<script>
	export default {
		props:["userInfo"],
		data() {
			return {
				show:false,
				userMessage:{
					avatarUrl:'https://6472-dreamland2-a708ef-1259161827.tcb.qcloud.la/bg-image/default-avatar.PNG',
					name:'立即登录',
					class:"登录后可发布信息"
				}
			};
		},
		methods:{
			// 登录验证的弹出框哦
			isShowDialogLogin(){
				this.show = true
				wx.lin.showDialog({
				  type:"confirm",     
				  title:"温馨提示",
				  content:"点击进行身份验证" ,
				  success: (res) => {
				    if (res.confirm) {
					  uni.navigateTo({
					  	url:'../login/login'
					  })
				    } else if (res.cancel) {
				      console.log('用户点击取消')
				    }
				  }
				})
			},
			// 登录
			login(){
				const that = this;
				uni.getStorage({
					key:'openid',
					fail() {
						that.isShowDialogLogin()
					}
				})
			}
		},
		created() {
			if(this.userInfo){
				this.userMessage = this.userInfo
			}
		}
	}
</script>

<style lang="scss">
/* miniprogram/pages/home/home.wxss */


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

.UCenter-bg .gif-wave{
  position: absolute;
  width: 100%;
  bottom: 0;
  left: 0;
  z-index: 99;
  mix-blend-mode: screen;  
  height: 100rpx;   
}

map,.mapBox{
  left: 0;
  z-index: 99;
  mix-blend-mode: screen;  
  height: 100rpx;   
}

map,.mapBox{
  width: 750rpx;
  height: 300rpx;
}


.bar-wrapper{
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
}

.hline{
  margin-bottom: 120rpx;
}
</style>
