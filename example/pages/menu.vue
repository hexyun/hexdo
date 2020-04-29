<template>
  <div id="menu">
    <p>多点菜单</p>
    <duodian-menu
      :list="duodian_list"
      :active-group.sync="duodian_activeGroup"
      :active-item.sync="duodian_activeItem"
      @selectthisgroup="selectThisGroup"
      @selectthisitem="selectThisItem"
    ></duodian-menu>
  </div>
</template>

<script>
export default {
  name: 'menu',
  data() {
    return {
      duodian_list: [],
      duodian_activeGroup: '',
      duodian_activeItem: ''
    }
  },
  ready() {
    for (let i = 0; i < 10; i++) {
      let arr = []
      for (let j = 0; j < 3; j++) {
        arr.push({
          name: `分类${i + 1}菜单${j + 1}`,
          id: i + 1 + '' + (j + 1)
        })
      }
      this.duodian_list.push({
        name: `分类${i + 1}`,
        id: i + 1 + '',
        children: arr
      })
    }
    this.duodian_activeGroup = this.duodian_list[0].id
    this.duodian_activeItem =
      this.duodian_list[0].children && this.duodian_list[0].children.length
        ? this.duodian_list[0].children[0].id
        : ''
  },
  methods: {
    selectThisGroup() {
      console.log(this.duodian_activeGroup)
    },
    selectThisItem() {
      console.log(this.duodian_activeGroup, this.duodian_activeItem)
    }
  }
}
</script>

<style lang="css">
#menu {
  margin-top: 60px;
}
</style>
