<template>
<uni-shadow-root class="dist-message-index"><view :class="'l-message l-class '+('l-message-'+type)+' '+(status?'l-message-show':'')" :style="'z-index:'+(zIndex)+';top:'+(top)+'rpx'">
  <block v-if="status">
    <view style="margin-right:15rpx">
      <l-icon :name="icon?icon:type" :size="iconSize" :color="type==='warning'?'#333':iconColor"></l-icon>
    </view>
    <image v-if="image" :src="image" class="l-message-image l-class-image"></image>
    {{content}}
    <slot></slot>
  </block>
</view></uni-shadow-root>
</template>

<script>
import LIcon from '../icon/index.vue'
global['__wxVueOptions'] = {components:{'l-icon': LIcon}}

global['__wxRoute'] = 'dist/message/index'
import zIndex from"../behaviors/zIndex";import watchShow from"../behaviors/watchShow";import validator from"../behaviors/validator";Component({behaviors:[zIndex,watchShow,validator],externalClasses:["l-class","l-image-class"],properties:{show:Boolean,icon:String,iconColor:{type:String,value:"#fff"},iconSize:{type:String,value:"28"},image:String,content:String,type:{type:String,value:"primary",options:["primary","warning","success","error"]},duration:{type:Number,value:1500},openApi:{type:Boolean,value:!0},top:{type:Number,value:0}},data:{status:!1},observers:{icon:function(){}},attached(){this.initMessage()},pageLifetimes:{show(){this.initMessage()}},methods:{initMessage(){wx.lin=wx.lin||{},wx.lin.showMessage=(t={})=>{const{content:e="",icon:i="",image:a="",type:s="primary",duration:o=1500,success:n=null,top:r=0}=t;return this.data.success=n,this.setData({content:e,icon:i,image:a,duration:o,type:s,top:r}),this.changeStatus(),this},wx.lin.hideMessage=()=>{this.setData({status:!1})}}}});
export default global['__wxComponents']['dist/message/index']
</script>
<style platform="mp-weixin">
.l-message{width:750rpx;height:72rpx;border-radius:0rpx 0rpx 16rpx 16rpx;display:flex;justify-content:center;align-items:center;position:fixed;left:50%;font-size:28rpx;color:#fff;opacity:0;box-shadow:0rpx 6rpx 16rpx 0rpx rgba(217,212,191,.5);transform:translateX(-50%) translateZ(0) translateY(-100%);transition:all .4s ease-in-out}.l-message-success{background-color:#34bfa3}.l-message-error{background-color:#f4516c}.l-message-warning{background-color:#ffe57f;color:#333}.l-message-primary{background-color:#3963bc}.l-message-show{transform:translateX(-50%) translateZ(0) translateY(0);opacity:1}.l-message-image{width:30rpx;height:30rpx;margin-right:15rpx}
</style>