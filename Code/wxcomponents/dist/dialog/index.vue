<template>
<uni-shadow-root class="dist-dialog-index"><l-popup :show="show" animation="show" contentAlign="center" :locked="true" @lintap="onDialogTap" l-bg-class="l-bg-class" :z-index="zIndex">
    <view class="dialog-container l-class" :style="'margin-bottom:'+(distance)+'px'">
        <view class="dialog-title l-title-class" :style="'color:'+(titleColor)" v-if="showTitle">{{title}}</view>
        <view class="dialog-content l-content-class" :style="'color:'+(contentColor)">
            <slot></slot>
            {{content}}
        </view>
        <view class="dialog-btn-group">
            <view class="dialog-btn-cancel l-cancel-class" :style="'color: '+(cancelColor)" @click.stop.prevent="onCancelTap" :hover-class="isHover?'group-hover':''" v-if="type==='confirm'">{{cancelText}}</view>
            <view class="dialog-btn-confirm l-confirm-class" :style="'color: '+(confirmColor)" :hover-class="isHover?'group-hover':''" @click.stop.prevent="onConfirmTap">{{confirmText}}</view>
        </view>
    </view>
</l-popup></uni-shadow-root>
</template>

<script>
import LPopup from '../popup/index.vue'
global['__wxVueOptions'] = {components:{'l-popup': LPopup}}

global['__wxRoute'] = 'dist/dialog/index'
import computeOffset from"../behaviors/computeOffset";import zIndex from"../behaviors/zIndex";import hover from"../behaviors/hover";import validator from"../behaviors/validator";Component({behaviors:[computeOffset,zIndex,hover,validator],externalClasses:["l-class","l-title-class","l-content-class","l-confirm-class","l-cancel-class","l-bg-class"],properties:{show:{type:Boolean,value:!1},type:{type:String,value:"alert",options:["alert","confirm"]},title:{type:String,value:"提示"},showTitle:{type:Boolean,value:!0},content:{type:String,value:""},locked:{type:Boolean,value:!0},confirmText:{type:String,value:"确定"},confirmColor:{type:String,value:"#3683d6"},cancelText:{type:String,value:"取消"},cancelColor:{type:String,value:"#45526b"},titleColor:String,contentColor:{type:String,value:"rgba(89,108,142,1)"},openApi:{type:Boolean,value:!0}},data:{success:null,fail:null},attached(){this.data.openApi&&this.initDialog()},pageLifetimes:{show(){this.data.openApi&&this.initDialog()}},methods:{initDialog(){wx.lin=wx.lin||{},wx.lin.showDialog=t=>{const{type:e="alert",title:o="提示",showTitle:l=!0,content:s="",locked:a=!0,confirmText:i="确定",contentColor:n="rgba(89,108,142,1)",cancelColor:c="#45526b",cancelText:r="取消",confirmColor:h="#3683d6",success:p=null,fail:m=null}=t;return this.setData({type:e,title:o,showTitle:l,content:s,locked:a,confirmText:i,cancelColor:c,cancelText:r,confirmColor:h,contentColor:n,show:!0,fail:m,success:p}),this}},onConfirmTap(){const{success:t}=this.data;t&&t({confirm:!0,cancel:!1,errMsg:"showDialog: success"}),this.setData({show:!this.data.show}),this.triggerEvent("linconfirm","confirm",{bubbles:!0,composed:!0})},onCancelTap(){const{success:t}=this.data;t&&t({confirm:!1,cancel:!0,errMsg:"showDialog: success"}),this.setData({show:!this.data.show}),this.triggerEvent("lincancel","cancel",{bubbles:!0,composed:!0})},onDialogTap(){!0!==this.data.locked&&this.setData({show:!this.data.show}),this.triggerEvent("lintap",!0,{bubbles:!0,composed:!0})}}});
export default global['__wxComponents']['dist/dialog/index']
</script>
<style platform="mp-weixin">
.dialog-container{display:flex;flex-direction:column;align-items:center;width:520rpx;background:#fff;border-radius:12rpx}.dialog-title{font-size:32rpx;font-family:PingFangSC-Regular;color:#45526b;line-height:44rpx;margin-top:30rpx;padding:0 25rpx;text-align:center}.dialog-content{font-size:28rpx;font-family:PingFangSC-Regular;line-height:40rpx;margin-top:30rpx;margin-bottom:30rpx;display:flex;flex-direction:column;align-items:center;padding:0 25rpx}.dialog-btn-group{width:100%;height:80rpx;display:flex;flex-direction:row;justify-content:space-between;align-items:center}.dialog-btn-cancel{font-size:28rpx;height:80rpx;width:259rpx;border-right:2rpx solid #f3f3f3;display:flex;flex-direction:row;align-items:center;justify-content:center;border-top:2rpx solid #f3f3f3}.dialog-btn-confirm{font-size:28rpx;flex:1;color:#3963bc;height:80rpx;display:flex;flex-direction:row;align-items:center;justify-content:center;border-top:2rpx solid #f3f3f3}.active{color:#3683d6}.leave{color:#45526b}.group-hover{opacity:.8}
</style>