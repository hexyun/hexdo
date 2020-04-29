<template>
  <div class="duodian_menu">
    <div class="duodian_menu_group" v-for="group in list" :key="group.id">
      <div
        class="group"
        v-show="activeGroup != group.id"
        @click="selectThisGroup(group)"
      >
        {{ group.name }}
      </div>
      <div class="items" v-show="activeGroup == group.id">
        <div class="groupNameWrap">
          <div
            class="name"
            :class="{ no_children: !group.children || !group.children.length }"
          >
            {{ group.name }}
          </div>
        </div>
        <div
          class="item"
          v-for="item in group.children"
          :key="item.id"
          :class="{ active: activeItem == item.id }"
          @click="selectThisItem(item)"
        >
          {{ item.name }}
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'duodian-menu',
  props: {
    list: {
      type: Array,
      default: function () {
        return []
      }
    },
    activeGroup: {
      type: String,
      default: ''
    },
    activeItem: {
      type: String,
      default: ''
    }
  },
  methods: {
    selectThisGroup(group) {
      this.activeGroup = group.id
      this.activeItem =
        group.children && group.children.length ? group.children[0].id : ''
      this.$emit('selectthisgroup')
    },
    selectThisItem(item) {
      this.activeItem = item.id
      this.$emit('selectthisitem')
    }
  }
}
</script>
<style scoped>
.fontstyle {
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: rgba(51, 39, 39, 0.65);
  line-height: 20px;
  padding: 10px 0;
}
.duodian_menu {
  height: 400px;
  width: 86px;
  overflow: auto;
  cursor: pointer;
  user-select: none;
}
.duodian_menu::-webkit-scrollbar {
  display: none;
}
.duodian_menu .duodian_menu_group {
  width: 100%;
  height: auto;
  text-align: center;
}
.duodian_menu .duodian_menu_group .group {
  background: #f6f7f5;
  width: 90%;
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: rgba(51, 39, 39, 0.65);
  line-height: 20px;
  padding: 10px 0;
}
.duodian_menu .duodian_menu_group .items {
  width: 95%;
  box-shadow: 0px 0px 6px 0px rgba(0, 0, 0, 0.09);
  border-radius: 0px 4px 4px 0px;
  background: #fff;
}
.duodian_menu .duodian_menu_group .items .groupNameWrap {
  padding: 0 10px;
}
.duodian_menu .duodian_menu_group .items .groupNameWrap .name {
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: rgba(51, 39, 39, 0.65);
  line-height: 20px;
  padding: 10px 0;
  font-weight: bold;
  background: #fff;
  border-bottom: 1px solid #ededed;
}
.duodian_menu .duodian_menu_group .items .groupNameWrap .no_children {
  border-bottom: none;
}
.duodian_menu .duodian_menu_group .items .item {
  font-size: 12px;
  font-family: PingFangSC-Regular, PingFang SC;
  font-weight: 400;
  color: rgba(51, 39, 39, 0.65);
  line-height: 20px;
  padding: 10px 0;
}
.duodian_menu .duodian_menu_group .items .active {
  border-left: 3px solid #00cf84;
  color: #00cf84;
  font-weight: bold;
}
</style>
