<template>
  <div class="page-toast">
    <h1 class="page-title">Notice Bar</h1>
      <mt-notice-bar v-ref:notice :list="list"></mt-notice-bar>
  </div>
    <p>{{x}}</p>
</template>

<style lang="css">
  @component-namespace page {
    @component notice {
      @descendent wrapper {
        padding: 0 20px;
        position: absolute 50% * * *;
        width: 100%;
        transform: translateY(-50%);
        button:not(:last-child) {
          margin-bottom: 20px;
        }
      }
    }
  }
</style>

<script type="text/babel">
  import { noticeBar } from 'src/index';

  export default {
    data() {
      return {
        list: Array.apply(null, {length: 3}).map((_, i) => `消息${i + 1}:  听说 ${i + 1}号长得很好看`)
      }
    },
    ready(){
      console.log(this.$refs.notice._data)
    },
    computed: {
      x(){
        const v = Object.assign({}, this.$refs.notice._data)
        delete v.copyData
        delete v.timer
        delete v.itemStyle
        return JSON.stringify(v)
      }
    }
  };
</script>
