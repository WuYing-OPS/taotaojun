<template>
<uni-shadow-root class="dist-input-index"><label :class="'form-item '+(disabled? "disabled": "")+' l-class form-item-'+(labelLayout)" :style="'width:'+(width===null?'auto':width+'rpx')">
  <view class="mask" v-if="disabled"></view>
  <view class="row l-row-class" :hidden="showRow ? '' : 'hidden'" :style="'width:'+(width)+'rpx;'"></view>
  <view v-if="label && !labelCustom" :hidden="hideLabel" :class="'form-label l-label-class form-label-'+(labelLayout)" :style="(labelLayout !== "top" ? "width:"+ labelWidth+ "rpx;" : "")+' height:'+(labelLayout=== "top" ? labelWidth + "rpx" : "")">
    <text><text class="text-require" v-if="required">* </text>{{label}}<text v-if="colon">：</text>
    </text>
  </view>
  <view v-else :hidden="hideLabel" :class="'form-label l-label-class form-label-'+(labelLayout)" :style="(labelLayout !== "top" ? "width:"+ labelWidth+ "rpx;" : "")+' height:'+(labelLayout=== "top" ? labelWidth + "rpx" : "")">
    <slot name="left"></slot>
  </view>
  
  <input :class="'input '+(hideLabel?'hideLabel':'')+' l-input-class'" :value="value" :type="type" :password="type==='password'" :placeholder="placeholder" :maxlength="maxlength" placeholder-class="pls-class" :placeholder-style="placeholderStyle" :disabled="disabled" :focus="focus" @input="handleInputChange" @focus="handleInputFocus" @blur="handleInputBlur" @confirm="handleInputConfirm"></input>
  <l-icon v-if="showEye&&value" name="eye" @click.native.stop.prevent="onTapEyeIcon" size="40" :l-class="'l-eye l-eye-'+(type)"></l-icon>
  <view class="close" v-if="clear&&value" mut-bind:tap="onClearTap">
    <view class="close-icon">
      <l-icon name="close" color="#fff" size="16"></l-icon>
    </view>
  </view>
  <slot name="right"></slot>
  <l-error-tip l-error-text-class="l-error-text l-error-text-class" :errorText="errorText" v-if="errorText"></l-error-tip>
</label></uni-shadow-root>
</template>

<script>
import LIcon from '../icon/index.vue'
import LErrorTip from '../error-tip/index.vue'
global['__wxVueOptions'] = {components:{'l-icon': LIcon,'l-error-tip': LErrorTip}}

global['__wxRoute'] = 'dist/input/index'
import eventBus from"../core/utils/event-bus.js";import validator from"../behaviors/validator";import rules from"../behaviors/rules";Component({options:{multipleSlots:!0},behaviors:["wx://form-field",validator,rules],externalClasses:["l-class","l-label-class","l-error-text","l-error-text-class","l-input-class","l-row-class"],properties:{label:String,hideLabel:Boolean,labelCustom:Boolean,showRow:{type:Boolean,value:!0},required:Boolean,placeholder:String,type:{type:String,value:"text",options:["text","idcard","digit","password","number"]},value:String,colon:Boolean,focus:Boolean,clear:Boolean,maxlength:{type:Number,value:140},width:{type:Number,value:null},labelWidth:{type:Number,value:200},labelLayout:{type:String,value:"left",options:["left","right"]},disabled:Boolean,placeholderStyle:String,showEye:{type:Boolean,value:!1}},data:{},attached(){},methods:{handleInputChange(e){const{detail:t={}}=e,{value:a=""}=t;this.setData({value:a}),eventBus.emit("lin-form-change-"+this.id,this.id),this.triggerEvent("lininput",e.detail)},handleInputFocus(e){this.triggerEvent("linfocus",e.detail)},handleInputBlur(e){this.validatorData({[this.data.name]:e.detail.value}),eventBus.emit("lin-form-blur-"+this.id,this.id),this.triggerEvent("linblur",e.detail)},handleInputConfirm(e){const{detail:t={}}=e,{value:a=""}=t;this.setData({value:a}),this.triggerEvent("linconfirm",e.detail)},onClearTap(e){this.setData({value:""}),this.triggerEvent("linclear",e.detail)},getValues(){return this.data.value},reset(){this.setData({value:""})},onTapEyeIcon(){const e=this.data.type;"text"===e?this.setData({type:"password"}):"password"===e&&this.setData({type:"text"})}}});
export default global['__wxComponents']['dist/input/index']
</script>
<style platform="mp-weixin">
.form-item{position:relative;font-size:28rpx;color:#333;height:88rpx;display:flex;flex-direction:row;align-items:center;padding-right:25rpx;box-sizing:border-box}.row{position:absolute;bottom:0;right:0;height:2rpx;width:730rpx;background:#f3f3f3}.text-require{color:#e23;vertical-align:middle}.form-label{display:flex;flex-direction:row;align-items:center;height:88rpx;padding-left:25rpx;padding-right:15rpx;box-sizing:border-box}.disabled{color:#9a9a9a!important}.mask{position:absolute;z-index:999;height:100%;width:100%}.form-label-right{justify-content:flex-end}.form-label-left{justify-content:flex-start}.input{height:100%;line-height:100%;flex:1}.close{height:36rpx;width:36rpx;background:#ddd;display:flex;flex-direction:row;align-items:center;justify-content:center;border-radius:50%;margin-right:20rpx}.pls-class{color:#9a9a9a}.hideLabel{padding-left:25rpx}.l-eye{padding:10rpx}.l-eye-text{color:#3963bc!important}.l-eye-password{color:rgba(57,99,188,.6)!important}
</style>