<template>
  <!-- 最底层的可视区容器 -->
  <div ref="list" class="infinite-list-container" @scroll="scrollEvent($event)">
    <!-- 中间的可滚动区域，z-index=-1，高度和真实列表相同，目的是出现相同的滚动条 -->
    <div class="infinite-list-phantom" :style="{ height: listHeight + 'px' }"></div>
    <!-- 最上层的可视区列表，数据和偏移距离随着滚动距离的变化而变化 -->
    <div class="infinite-list" :style="{ transform: getTransform }">
      <div
        class="infinite-list-item"
        v-for="item in visibleData"
        :key="item.id"
        :style="{ height: itemSize + 'px' }"
      >
        {{ item.label }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'virtualList',
  props: {
    //列表数据
    items: {
      type: Array,
      default: () => []
    },
    //列表项高度
    itemSize: {
      type: Number,
      default: 50
    },
    bufferScale:{
        type:Number,
        default: 10
    }
  },
  computed: {
    aboveCount() {
        // console.log('ac', Math.min(this.start,this.bufferScale * this.visibleCount))
        return Math.min(this.start,this.bufferScale + this.visibleCount)
    },
    belowCount(){
        // console.log('ab', Math.min(this.items.length - this.end,this.bufferScale * this.visibleCount))
        return Math.min(this.items.length - this.end, this.bufferScale + this.visibleCount);
    },
    //列表总高度
    listHeight() {
      return this.items.length * this.itemSize
    },
    //可视区列表的项数
    visibleCount() {
    //   console.log('count', Math.ceil(this.screenHeight / this.itemSize))
      return Math.ceil(this.screenHeight / this.itemSize)
    },
    //可视区列表偏移距离对应的样式
    getTransform() {
      return `translate3d(0,${this.startOffset}px,0)`
    },
    //获取可视区列表数据
    visibleData() {
        console.log(this.end)
      return this.items.slice(this.start > 0 ? this.start : 0, Math.min(this.end, this.items.length))
    }
  },
  mounted() {
    this.screenHeight = this.$refs.list.clientHeight
    this.start = 0 - this.bufferScale
    this.end = (this.start > 0 ? this.start : 0) + this.visibleCount + this.bufferScale
  },
  data() {
    return {
      screenHeight: 0, //可视区域高度
      startOffset: 0, //偏移距离
      start: 0, //起始索引
      end: 0 //结束索引
    }
  },
  methods: {
    scrollEvent() {
      //当前滚动位置
      let scrollTop = this.$refs.list.scrollTop
      //此时的开始索引
      this.start = Math.floor(scrollTop / this.itemSize) - this.bufferScale
      //此时的结束索引
      this.end = (this.start > 0 ? this.start : 0) + this.visibleCount + this.bufferScale
      //此时的偏移距离
      this.startOffset = (scrollTop - (scrollTop % this.itemSize) - this.itemSize * this.bufferScale) > 0 ? (scrollTop - (scrollTop % this.itemSize) - this.itemSize * this.bufferScale):0
    }
  }
}
</script>

<style scoped>
.infinite-list-container {
  height: 100%;
  overflow: auto;
  position: relative;
}

.infinite-list-phantom {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  z-index: -1;
}

.infinite-list {
  left: 0;
  right: 0;
  top: 0;
  position: absolute;
}

.infinite-list-item {
  line-height: 50px;
  text-align: center;
  color: #555;
  border: 1px solid #ccc;
  box-sizing: border-box;
}
</style>