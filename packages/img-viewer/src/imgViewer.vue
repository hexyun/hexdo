<template>

    <div v-show="show" class="mt-img__text">{{ i }}/{{list.length}}</div>
    <mt-popup :visible.sync="show" popup-transition="popup-fade" class="mt-img-viewer">
        <swiper class="mt-img-swiper" v-ref:swiper
                :direction="direct"
                :mousewheel-control="true"
                :performance-mode="false"
                :pagination-visible="false"
                :pagination-clickable="false"
                :loop="false"
                @slide-change-start="onSlideChangeStart"
                @slide-change-end="onSlideChangeEnd">
            <div class="mt-img__item" track-by="$index" v-for="item in list"><img :src="key ? item[key] : item" alt=""></div>
        </swiper>
    </mt-popup>

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
    index: {
      type: [Number, String],
      default: 0
    },
    show: {
      type: Boolean,
      default: true
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
    return {i: this.index}
  },
  watch: {
    index(val, old) {
      if(val != old) this.$refs.swiper.setPage(val)
    }
  },
  methods: {
    onSlideChangeStart(p){
      this.$emit('start', p)
    },
    onSlideChangeEnd(p){
      this.i = p
      this.$emit('end', p)
    },
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
