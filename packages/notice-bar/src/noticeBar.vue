<template>
  <div class="mint-notice-bar">
      <div :class="parentClass" v-el:wrapper class="mint-notice__wrapper">
          <div track-by="$index" :style="itemStyle" class="mint-notice__item" v-for="item in copyData"><p>{{item}}</p></div>
      </div>
  </div>
</template>

<script>
/**
 * mt-notice-bar
 * @module components/notice-bar
 * @desc 滚动消息组件，处理滚动的文本等等
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
    start: {
      type: Boolean,
      default: true
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
    loop(duration = 1, cb) {
      if(!this.start) return
      if(typeof cb === 'function') cb()
      this.timer = setTimeout(this.loop, this._lock ? 0 : duration * 1000, duration, cb)
    },
    start() {
      const el = this.$els.wrapper
      const h = parseInt(window.getComputedStyle(el.parentNode, null)['height'])
      this.itemStyle = {height: h}
      el.style.height = `${h * this.copyData.length}px`
      this.loop(this.duration, () => {
        this.i++
        if(this.i === this.copyData.length) {
          this.i = 0
          this._lock = true
          this.parentClass.push('notransition')
        } else {
          this.parentClass = []
          this._lock = false
        }
        el.style.transform = `translateY(${-1 * h * this.i}px)`

      })
    },
    stop() {
      if(this.timer) {
          clearTimeout(this.timer)
          this.timer = null
      }
    }
  },
  beforeDestroy() {
    this.stop()
  }
};
</script>

<style lang="css">
  @import "../../../src/style/var.css";

  @component-namespace mint {
    @component notice-bar {
        height: 30px;
        line-height: 30px;
        overflow: hidden;
        display: flex;
        text-align: center;
        & .mint-notice__wrapper{
            height: 100%;
            transition: transform .5s ease-in-out;
        }
    }
  }
  .notransition {
      transition: none !important;
  }
</style>
