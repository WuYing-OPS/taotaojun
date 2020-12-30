<template>
<uni-shadow-root class="dist-mask-index"><view :class="'container-mask '+(center? 'center' : '')+' l-class'" :hidden="show? '' :  'hidden'" @touchmove.stop.prevent="doNothingMove" @click.stop.prevent="onMaskTap" :style="'z-index:'+(zIndex)+'; '+(show? 'background: rgba(0,0,0,' + opacity+ ')' :  '')">
  
  <view class="mask-content l-mask-class">
    <slot></slot>
  </view>
</view></uni-shadow-root>
</template>

<script>

global['__wxVueOptions'] = {components:{}}

global['__wxRoute'] = 'dist/mask/index'
import zIndex from"../behaviors/zIndex";Component({behaviors:[zIndex],externalClasses:["l-class","l-mask-class"],properties:{show:{type:Boolean,value:!1},opacity:{type:[String,Number],value:.4},zIndex:{type:Number,value:99},center:{type:Boolean,value:!1},locked:{type:Boolean,value:!0},fullScreen:{type:String,value:""},NavColor:{type:String,value:""}},data:{},methods:{doNothingMove(){},onMaskTap(){!0!==this.data.locked&&this.setData({show:!1}),this.triggerEvent("lintap",!0,{bubbles:!0,composed:!0})}},attached:function(){}});
export default global['__wxComponents']['dist/mask/index']
</script>
<style platform="mp-weixin">
.container-mask{position:fixed;top:0;left:0;width:100%;height:100%;transition:all .3s}.mask-bg{height:100%;width:100%;background:#fff;z-index:99}.mask-content{display:inline-block;z-index:101;overflow:hidden}.center{display:flex;flex-direction:row;align-items:center;justify-content:center}
</style>