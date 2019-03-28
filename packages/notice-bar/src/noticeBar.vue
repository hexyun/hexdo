<template>
  <div class="mint-notice-bar">
      <div v-el:wrapper class="mint-notice__wrapper" :class="parentClass">
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
      default: 2
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
      parentClass: ['notransition'],
      _lock: false
    }
  },
  ready() {
    this.start()
    document.addEventListener('visibilitychange', () => {
      if(document.visibilityState === 'hidden') {
        this.stop()
      } else {
        this.start()
      }
    })
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
      this.timer = setTimeout(this.loop, this._lock ? 1000 / 16 : duration * 1000, duration, cb)
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
          this.parentClass.pop()
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
        position: relative;
        & .mint-notice__wrapper{
            position: absolute;
            left:0;
            max-height: 0;
            height: 100%;
            transition: all .5s ease-in-out;
            transform: translateZ(0);
        }
    }
      .notransition{
          -webkit-transition-property: none !important;
          -moz-transition-property: none !important;
          -o-transition-property: none !important;
          transition-property: none !important;
      }
  }
</style>
