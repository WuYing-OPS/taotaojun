<template>
<uni-shadow-root class="dist-grid-index"><view class="l-grid l-class" @click="tapGrid">
    <view v-for="(item,index) in (gridItems)" :key="item.key" @click="tapGridItem" :data-grid-index="item.index" :class="'l-grid-item l-class-grid l-grid-class '+(index%rowNum !== rowNum-1 &&(showBorder||showColBorder) ? 'side-grid':'')+' '+((index<gridItems.length-(gridItems.length%rowNum||rowNum)) &&(showBorder||showRowBorder)? 'center-grid':'')" :style="'min-width:'+(100/rowNum)+'%;'">
        <slot :name="item.key"></slot>
    </view>
</view></uni-shadow-root>
</template>

<script>
import LGridItem from '../grid-item/index.vue'
global['__wxVueOptions'] = {components:{'l-grid-item': LGridItem}}

global['__wxRoute'] = 'dist/grid/index'
import hover from"../behaviors/hover";Component({options:{multipleSlots:!0},behaviors:[hover],relations:{"../grid-item/index":{type:"child",linked(){this.initGrids()},unlinked(){this.initGrids()}}},externalClasses:["l-class","l-class-grid","l-grid-class"],properties:{rowNum:{type:String,value:3},showBorder:Boolean,showColBorder:Boolean,showRowBorder:Boolean},data:{gridItems:[],childNum:0,currentIndex:-1,currentCell:-1},ready(){this.initGrids()},lifetimes:{show(){}},methods:{initGrids(){let e=this.getRelationNodes("../grid-item/index");if(this.data.childNum===e.length)return;const t=e.map((e,t)=>(e.setData({index:t}),{index:t,key:e.data.key,cell:e.data.cell}));this.setData({gridItems:t,childNum:e.length})},tapGridItem(e){const{gridIndex:t}=e.target.dataset;this.setData({currentIndex:t,currentCell:this.data.gridItems[t].cell})},tapGrid(){this.triggerEvent("lintap",{index:this.data.currentIndex,cell:this.data.currentCell},{bubbles:!0,composed:!0}),this.setData({currentIndex:-1,currentCell:-1})}}});
export default global['__wxComponents']['dist/grid/index']
</script>
<style platform="mp-weixin">
.l-grid{display:flex;width:inherit;flex-wrap:wrap}.l-grid .l-grid-item{display:flex;justify-content:center;flex-direction:column;text-align:center;box-sizing:border-box;border-style:solid;border-color:#ededed;border-width:0}.l-grid .center-grid{border-bottom-width:2rpx}.l-grid .side-grid{border-right-width:2rpx}
</style>