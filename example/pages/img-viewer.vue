<template>
  <div class="page-toast">
    <h1 class="page-title">Img Viewer</h1>
      <mt-img-viewer :index="index" v-ref:viewer :list="list"></mt-img-viewer>
  </div>
</template>

<style lang="css">
</style>

<script type="text/babel">
  export default {
    data() {
      return {
        list: [
          'https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=1791235467,4027597102&fm=26&gp=0.jpg',
          'https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=685370796,2579364299&fm=11&gp=0.jpg',
          'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2131968871,999564851&fm=26&gp=0.jpg',
          'https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=174877223,346478566&fm=26&gp=0.jpg',
          'https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1244803303,3072766805&fm=26&gp=0.jpg'
        ],
        index: 1
      }
    }
  };
</script>
