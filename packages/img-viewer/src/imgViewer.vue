<template>
    <div>
        <div v-show="visible" class="mt-img__text">{{ i }}/{{list.length}}</div>
        <mt-popup :visible="visible || lock" popup-transition="popup-fade" class="mt-img-viewer">
            <swiper class="mt-img-swiper" v-ref:swiper
                    :direction="direct"
                    :mousewheel-control="false"
                    :performance-mode="false"
                    :pagination-visible="false"
                    :pagination-clickable="false"
                    :loop="false"
                    @slide-change-start="onSlideChangeStart"
                    @slide-revert-start="onRevertStart"
                    @slide-revert-end="onRevertEnd"
                    @slider-move="onMove"
                    @slide-change-end="onSlideChangeEnd">
                <div @mousedown.prevent="select(item, index, list)" class="mt-img__item" track-by="$index" v-for="( index, item) in list"><img :src="key ? item[key] : item" alt=""></div>
            </swiper>
        </mt-popup>
    </div>

</template>

<script>
/**
 * mt-img-viewer
 * @module components/img-viewer
 * @desc 图片预览组件，支持PC和移动端等等
 * @example
 *
 */

import Swiper from 'vue-swiper'

export default {
  name: 'mt-img-viewer',
  props: {
    direction: {
      type: String,
      default: 'x'
    },
    list:{
      type: Array,
      default: () => []
    },
    key: {
      type: String,
      default: ''
    },
    page: {
      type: [Number, String],
      default: 1
    },
    show: {
      type: Boolean,
      default: true
    },
    lock: {
      type: Boolean,
      default: false
    },
    selectItem: {
      type: Function,
      default: function d() {
        return function() {}
      }
    }
  },
  components: {
    Swiper
  },
  computed: {
    direct() {
        return {
          x: 'horizontal',
          y: 'vertical'
        }[this.direction]
    }
  },
  data() {
    return {i: Math.min(Math.max(this.page, 1), this.list.length), visible: this.show, _lock: false, _moving: false}
  },
  ready(){
    this.setPage(this.i)
    this._lock = true
  },
  watch: {
    page(val, old) {
      if(val !== old) this.setPage(val)
    },
    show(val, old) {
      if(val !== old && !this.lock) this.visible = val
    }
  },
  methods: {
    setPage(v){
      this.$refs.swiper.setPage(Math.min(Math.max(v, 1), this.list.length), false)
    },
    onMove(e,mm) {
      this._moving = true
    },
    onRevertStart(p){
      if(this._lock && !this._moving) {
        this.visible = false
      }
    },
    onRevertEnd(p){
      this._moving = false
    },
    onSlideChangeStart(p){
      this.$emit('start', p)
    },
    onSlideChangeEnd(p){
      this._moving = false
      this.i = p
      this.$emit('end', p)
    },
    select(item, index, list) {
      this.selectItem(item, index, list)
    }
  },
  beforeDestroy() {
  }
};
</script>

<style lang="css">
  @import "../../../src/style/var.css";
  .mt-img-viewer{
      background: transparent;
  }
  .mt-img-viewer{
      width: 100%;
      max-width: 700px;
  }
  .mt-img-viewer + .v-modal{
      opacity: .9 !important;
  }
  .mt-img__item{
      -webkit-box-sizing: border-box;
      -moz-box-sizing: border-box;
      box-sizing: border-box;
  }
  .mt-img__item img{
      width: 100%;
      display: block;
  }
  .swiper .swiper-wrap{
      align-items: center;
  }
    .mt-img__text{
        position: fixed;
        z-index: 1005;
        top: 20px;
        left: 0;
        color: #fff;
        width: 100%;
        height: 30px;
        line-height: 30px;
        text-align: center;
    }

</style>
