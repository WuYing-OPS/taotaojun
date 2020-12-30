<template>
<uni-shadow-root class="dist-grid-item-index"><view :hover-class="isHover?'l-gird-item-hover':''" hover-start-time="20" hover-stay-time="50" class="l-grid-item l-grid-item-class grid-item" mut-bind:tap="tapGridItem">
    <slot></slot>
</view></uni-shadow-root>
</template>

<script>

global['__wxVueOptions'] = {components:{}}

global['__wxRoute'] = 'dist/grid-item/index'
import dataUtil from"../core/utils/data-util";Component({relations:{"../grid/index":{type:"parent"}},externalClasses:["l-grid-item","l-grid-item-class"],properties:{key:String,cell:{type:Object,value:{}}},data:{index:0,isHover:!0},attached(){},observers:{key:function(){const t=this.getRelationNodes("../grid/index")[0];t&&(t.setData({gridItems:[],childNum:0}),t.initGrids())}},lifetimes:{ready(){const t=this.getRelationNodes("../grid/index")[0];t&&dataUtil.setDiffData(this,{isHover:t.data.isHover})}},methods:{tapGridItem(){this.triggerEvent("linitemtap",{...this.data},{bubbles:!0,composed:!0})}}});
export default global['__wxComponents']['dist/grid-item/index']
</script>
<style platform="mp-weixin">
.grid-item{display:flex;justify-content:center;align-items:center;flex-direction:column;padding:32rpx 16rpx;box-sizing:border-box}.l-gird-item-hover{opacity:.8}
</style>