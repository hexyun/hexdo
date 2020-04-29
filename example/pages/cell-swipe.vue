<template>
  <div class="page-cell">
    <div class="page-title">Cell Swipe</div>
    <mt-cell-swipe
      v-for="n in list"
      :value="n"
      :right="rightButtons">
      <div class="hi">
      hello world
      </div>
    </mt-cell-swipe>
  </div>
</template>
<style>
.hi{
  height: 80px;
}
</style>
<script>
  export default {
    data () {
      return {
        list: [],
        rightButtons: []
      }
    },
    created() {
      this.rightButtons = [
        {
          content: '删除',
          style: { background: 'red', color: '#fff' },
          handler: (x) => console.log(x)
        }
      ];
    },
    ready () {
      setTimeout(_ => this.list = [1,2,5,10,30], 100)
    },

    methods: {
      leftButtonHandler(evt) {
        console.log(123);
      }
    }
  };
</script>
