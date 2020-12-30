<template>
<uni-shadow-root class="dist-transition-index"><view v-if="inited" :class="'l-transition l-class '+(classes)" :style="'-webkit-transition-duration:'+(currentDuration)+'ms; transition-duration:'+(currentDuration)+'ms; '+(display ? '' : 'display: none;')+' '+(customStyle)" @transitionend="onTransitionEnd">
  <slot></slot>
</view></uni-shadow-root>
</template>

<script>
import LIcon from '../icon/index.vue'
import LMask from '../mask/index.vue'
global['__wxVueOptions'] = {components:{'l-icon': LIcon,'l-mask': LMask}}

global['__wxRoute'] = 'dist/transition/index'
import transition from"../behaviors/transition";Component({behaviors:[transition(!0)],externalClasses:["l-class","l-enter-class","l-enter-active-class","l-enter-to-class","l-leave-class","l-leave-active-class","l-leave-to-class"],methods:{}});
export default global['__wxComponents']['dist/transition/index']
</script>
<style platform="mp-weixin">
.l-transition{transition-timing-function:ease}.l-fade-enter-active,.l-fade-leave-active{transition-property:opacity}.l-fade-enter,.l-fade-leave-to{opacity:0}.l-fade-down-enter-active,.l-fade-down-leave-active,.l-fade-left-enter-active,.l-fade-left-leave-active,.l-fade-right-enter-active,.l-fade-right-leave-active,.l-fade-up-enter-active,.l-fade-up-leave-active{transition-property:opacity,transform}.l-fade-up-enter,.l-fade-up-leave-to{transform:translate3d(0,100%,0);opacity:0}.l-fade-down-enter,.l-fade-down-leave-to{transform:translate3d(0,-100%,0);opacity:0}.l-fade-left-enter,.l-fade-left-leave-to{transform:translate3d(-100%,0,0);opacity:0}.l-fade-right-enter,.l-fade-right-leave-to{transform:translate3d(100%,0,0);opacity:0}.l-slide-down-enter-active,.l-slide-down-leave-active,.l-slide-left-enter-active,.l-slide-left-leave-active,.l-slide-right-enter-active,.l-slide-right-leave-active,.l-slide-up-enter-active,.l-slide-up-leave-active{transition-property:transform}.l-slide-up-enter,.l-slide-up-leave-to{transform:translate3d(0,100%,0)}.l-slide-down-enter,.l-slide-down-leave-to{transform:translate3d(0,-100%,0)}.l-slide-left-enter,.l-slide-left-leave-to{transform:translate3d(-100%,0,0)}.l-slide-right-enter,.l-slide-right-leave-to{transform:translate3d(100%,0,0)}
</style>