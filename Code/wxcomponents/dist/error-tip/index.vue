<template>
<uni-shadow-root class="dist-error-tip-index"><view class="error-text l-error-text l-error-text-class" v-if="errorText">{{errorText}}</view></uni-shadow-root>
</template>

<script>

global['__wxVueOptions'] = {components:{}}

global['__wxRoute'] = 'dist/error-tip/index'
Component({externalClasses:["l-error-text","l-error-text-class"],properties:{errorText:String},data:{},methods:{}});
export default global['__wxComponents']['dist/error-tip/index']
</script>
<style platform="mp-weixin">
.error-text{font-size:22rpx;color:#f4516c;margin-right:20rpx;line-height:2}
</style>