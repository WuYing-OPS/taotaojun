<template>
<uni-shadow-root class="dist-card-index"><view :class="'l-class card-container '+('card-container-' + type)+' '+('card-container-' + type + '-' + position)+' '+(full?'card-container-full':'card-container-unfull')">
  <block v-if="type ==='primary' || type ==='cover'">
    <image v-if="(!plaintext)" :class="'l-img-class '+(full?'cover-img-full':'cover-img-unfull')+' '+('card-img-' + type)+' '+('card-img-' + type + '-' + position)" :mode="imageMode" lazy-load :src="image"></image>
    <view class="card-content">
      <text :class="'l-title-class card-title '+('card-title-' + type)">{{title}}</text>
      <slot></slot>
    </view>
  </block>
  <block v-if="type ==='avatar'">
    <view class="card-avatar-top">
      <view class="card-avatar-left">
        <image mode="aspectFill" :class="'l-img-class '+('card-img-' + type)" :src="image" lazy-load></image>
        <view class="card-avatar">
          <text :class="'l-title-class card-title '+('card-title-' + type)">{{title}}</text>
          <text class="describe">{{describe}}</text>
        </view>
      </view>
      <slot name="more"></slot>
    </view>
    <slot></slot>
  </block>
</view></uni-shadow-root>
</template>

<script>

global['__wxVueOptions'] = {components:{}}

global['__wxRoute'] = 'dist/card/index'
import validator from"../behaviors/validator";Component({externalClasses:["l-class","l-img-class","l-title-class"],options:{multipleSlots:!0},behaviors:[validator],properties:{image:String,title:String,describe:String,plaintext:Boolean,full:Boolean,position:{type:String,value:"left",options:["left","right"]},type:{type:String,value:"primary",options:["primary","avatar","cover"]},imageMode:{type:String,value:"scaleToFill"}},data:{},methods:{}});
export default global['__wxComponents']['dist/card/index']
</script>
<style platform="mp-weixin">
.card-container{margin:0 auto;padding:30rpx;box-sizing:border-box;background:#fff;display:flex}.card-container-full{width:100%}.card-container-unfull{width:92%;box-shadow:0 4rpx 20rpx 0 rgba(212,217,223,.5);border-radius:4px}.card-container-primary-left{flex-direction:row}.card-container-primary-right{flex-direction:row-reverse}.card-container-avatar{flex-direction:column}.card-container-cover{flex-direction:column}.cover-img-full{width:100%;height:260rpx}.cover-img-unfull{width:100%;height:260rpx}.card-img-primary{height:240rpx;width:240rpx;overflow:hidden}.card-img-primary-left{margin-right:40rpx}.card-img-primary-right{margin-left:40rpx}.card-img-avatar{height:60rpx;width:60rpx;border-radius:50%;margin-right:30rpx}.card-img-avatar{height:60rpx;width:60rpx;border-radius:50%;margin-right:30rpx}.card-content{position:relative;display:flex;flex-direction:column;flex:1}.card-title{overflow:hidden;text-overflow:ellipsis;color:#333}.card-title-primary{font-size:32rpx;display:-webkit-box;-webkit-box-orient:vertical;-webkit-line-clamp:2}.card-title-avatar{font-size:28rpx}.card-title-cover{font-size:30rpx;margin-top:10rpx}.card-title-avatar{font-size:28rpx}.card-avatar-top{display:flex;flex-direction:row;height:100rpx;align-items:center;justify-content:space-between}.card-avatar-left{display:flex;flex-direction:row;align-items:center}.card-avatar{display:flex;flex-direction:column}.describe{color:#666;font-size:24rpx}
</style>