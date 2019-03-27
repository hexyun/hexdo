<template>
    <mt-popup :visible.sync="popupVisible1" popup-transition="popup-fade">

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
export default {
  name: 'mt-notice-bar',
  props: {
    list:{
      type: Array,
      default: () => []
    },
    duration: {
      type: Number,
      default: 1
    },
    key: {
      type: String,
      default: ''
    }
  },
  data() {
    const list = this.list.slice(0)
    list.push(list[0])
    return {
      copyData: list,
      timer: null,
      i: -1,
      itemStyle: {},
      parentClass: [],
      _lock: false
    }
  },
  ready() {
    this.start()
  },
  watch: {
    start(val, old) {
      if(val !== old) {
        val ? this.start() : this.stop()
      }
    }
  },
  methods: {
  },
  beforeDestroy() {
  }
};
</script>

<style lang="css">
  @import "../../../src/style/var.css";

  @component-namespace mint {
    @component img-viewer {
        height: 30px;
        line-height: 30px;
        overflow: hidden;
        display: flex;
        text-align: center;
        & .mint-img__wrapper{
            height: 100%;
            transition: transform .5s ease-in-out;
        }
    }
  }
  .notransition {
      transition: none !important;
  }
</style>
