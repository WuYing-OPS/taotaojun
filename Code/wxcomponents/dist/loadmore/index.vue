<template>
<uni-shadow-root class="dist-loadmore-index"><slot name="content"></slot>
<view mut-bind:tap="onLoadmore" v-if="show">
  <view v-if="custom && type==='end'">
    <slot name="end"></slot>
  </view>
  <view v-else-if="custom && type==='loading'">
    <slot name="loading"></slot>
  </view>
  <view class="loading l-class" v-else>
    <view class="line loading-view" :style="'background-color:'+color" v-if="line"></view>
    <view class="rotate loading-view" :style="'border-color: '+(color)+';width:'+(size)+'rpx;height:'+(size)+'rpx'" v-if="type=='loading'"></view>
    <view class="loading-text l-loading-class loading-view" :style="'color:'+(color)+';font-size:'+(size)+'rpx'" v-if="type=='loading'">{{loadingText}}</view>
    <view class="loading-text l-end-class loading-view" :style="('color:'+color)+';font-size:'+(size)+'rpx'" v-if="type=='end'">{{endText}}</view>
    <view class="line l-line-class loading-view" :style="'background-color:'+color" v-if="line"></view>
  </view>
</view></uni-shadow-root>
</template>

<script>
import LLoading from '../loading/index.vue'
global['__wxVueOptions'] = {components:{'l-loading': LLoading}}

global['__wxRoute'] = 'dist/loadmore/index'
import validator from"../behaviors/validator";Component({externalClasses:["l-class","l-loading-class","l-end-class","l-line-class"],options:{multipleSlots:!0},behaviors:[validator],properties:{show:Boolean,custom:Boolean,line:Boolean,color:String,size:{type:String,value:"28"},type:{type:String,value:"loading",options:["loading","end"]},endText:{type:String,value:"我是有底线的~"},loadingText:{type:String,value:"加载中..."}},data:{},attached(){this._init()},pageLifetimes:{show(){this._init()}},methods:{_init(){wx.lin=wx.lin||{},wx.lin.showLoadmore=e=>{const{custom:o=!1,line:t=!1,color:i="",size:l="28",type:a="loading",endText:n="我是有底线的",loadingText:s="加载中..."}={...e};this.setData({custom:o,line:t,color:i,size:l,type:a,endText:n,loadingText:s,show:!0})},wx.lin.hideLoadmore=()=>{this.setData({show:!1})}},onLoadmore(){this.triggerEvent("lintap",{},{bubbles:!0,composed:!0})}}});
export default global['__wxComponents']['dist/loadmore/index']
</script>
<style platform="mp-weixin">
.loadmore-container{display:flex;flex-direction:column;background-color:transparent}.loading{display:flex;flex-direction:row;width:100%;height:72rpx;align-items:center;justify-content:center;background-color:transparent}.loading .loading-view:nth-child(2){margin-left:36rpx}.loading-text{color:#bbb;font-size:28rpx;margin:0 12rpx}.line{width:80rpx;height:2rpx;background-color:#d1d3d7}.rotate{border-radius:50%;animation:rotate .7s linear infinite;height:28rpx;width:28rpx;border-top:4rpx solid #bbb;border-right:4rpx solid transparent!important;border-bottom:4rpx solid #bbb;border-left:4rpx solid #bbb;margin-left:12rpx}@keyframes rotate{0%{transform:rotate(0)}100%{transform:rotate(360deg)}}
</style>