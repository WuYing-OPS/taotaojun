<template>
<uni-shadow-root class="dist-toast-index"><view :class="'container '+(mask?'containerShowMask':'containerNoMask')" :hidden="(!status)" :style="'z-index:'+(zIndex)">
  <view class="l-bg-class toast-bg" v-if="mask"></view>
  <view :class="'l-class toast toast-'+(placement || 'bottom')" :style="'padding-top:'+((placement  || 'bottom')=== 'bottom' ?  image || icon ? '25rpx': '': '')+';position:relative;left:'+(offsetX)+'rpx;top:'+(offsetY)+'rpx;margin-bottom:'+(distance)+'px'">
    <image class="l-image-class toast-icon" v-if="image" :src="image"></image>
    <l-icon :class="'l-icon-class toast-icon toast-icon-'+(icon === 'loading'?'loading':'')" v-else-if="icon && !image" :size="iconSize? iconSize : 60" :color="iconColor? iconColor: icon === 'success'? '#00C292' :  icon === 'error' ? '#F4516C' : '#ffffff'" :name="icon"></l-icon>
    <slot v-else></slot>
    <text :class="'toast-text l-title-class toast-text-'+(placement)" :style="placement || 'bottom' === 'bottom' ? icon || image? 'margin-top:10rpx' : '': ''">{{ title }}</text>
  </view>
</view></uni-shadow-root>
</template>

<script>
import LIcon from '../icon/index.vue'
import LMask from '../mask/index.vue'
global['__wxVueOptions'] = {components:{'l-icon': LIcon,'l-mask': LMask}}

global['__wxRoute'] = 'dist/toast/index'
import computeOffset from"../behaviors/computeOffset";import zIndex from"../behaviors/zIndex";import watchShow from"../behaviors/watchShow";Component({behaviors:[computeOffset,zIndex,watchShow],externalClasses:["l-bg-class","l-icon-class","l-class","l-image-class","l-title-class "],properties:{show:{type:Boolean,value:!1},title:String,icon:String,iconSize:String,iconColor:String,image:String,placement:{type:String,value:"bottom"},duration:{type:Number,value:1500},zIndex:{type:Number,value:777},center:{type:Boolean,value:!0},mask:{type:Boolean,value:!1},openApi:{type:Boolean,value:!0},offsetX:Number,offsetY:Number},data:{status:!1,success:"",fail:"",complete:""},observers:{icon:function(){}},attached(){this.data.openApi&&this.initToast()},pageLifetimes:{show(){this.data.openApi&&this.initToast(),this.offsetMargin()}},methods:{initToast(){wx.lin=wx.lin||{},wx.lin.showToast=(e={})=>{const{title:t="",icon:o="",image:s="",placement:i="bottom",duration:a=1500,center:n=!0,mask:l=!1,success:r=null,complete:c=null,offsetX:h=0,offsetY:f=0,iconSize:m="60",iconColor:p=""}=e;return this.setData({title:t,icon:o,image:s,placement:i,duration:a,center:n,mask:l,success:r,complete:c,offsetY:f,offsetX:h,iconSize:m,iconColor:p}),this.changeStatus(),this},wx.lin.hideToast=()=>{this.setData({status:!1})}},strlen(e){for(var t=0,o=0;o<e.length;o++){var s=e.charCodeAt(o);s>="0x0001"&&s<="0x007e"||"0xff60"<=s&&s<="0xff9f"?t++:t+=2}return t},doNothingMove(){},onMaskTap(){!0!==this.data.locked&&this.setData({fullScreen:"hide",status:"hide"}),this.triggerEvent("lintap",!0,{bubbles:!0,composed:!0})}}});
export default global['__wxComponents']['dist/toast/index']
</script>
<style platform="mp-weixin">
.container{position:fixed}.containerNoMask{left:50%;top:50%;transform:translate(-50%,-50%)}.containerShowMask{height:100%;width:100%;top:0;left:0;display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:999}.container .toast-bg{height:100%;width:100%;background:rgba(255,255,255,.5);position:absolute;top:0;left:0}.container .toast-top{flex-direction:column-reverse}.container .toast-right{flex-direction:row}.container .toast-bottom{flex-direction:column}.container .toast-left{flex-direction:row-reverse}.container .toast{display:flex;align-items:center;justify-content:center;max-width:400rpx;min-width:280rpx;min-height:88rpx;background:rgba(0,0,0,.7);border-radius:12rpx;color:#fff;font-size:28rpx;line-height:40rpx;box-sizing:border-box;padding:30rpx 50rpx;z-index:999}.container .toast .toast-icon{margin-top:20rpx;margin-bottom:20rpx}.container .toast .toast-icon-loading{animation:loading-fadein 1.5s linear 0s infinite}.container .toast .toast-text{display:inline-block;text-align:center}.container .toast .toast-text-right{display:inline-block;text-align:center;margin-left:20rpx}.container .toast .toast-text-left{display:inline-block;text-align:center;margin-right:20rpx}.container .toast .toast-text-top{margin-bottom:10rpx}@keyframes loading-fadein{0%{transform:rotate(0)}100%{transform:rotate(360deg)}}
</style>