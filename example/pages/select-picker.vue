<template>
  <div class="page-datetime">
    <h1 class="page-title">Select Picker</h1>
    <div class="page-datetime-wrapper">
      <mt-button @click="visible = true" size="large">点击弹出 Select Picker</mt-button>
    </div>
      <mt-select-picker :visible.sync="visible" :value="value" :list="list" @change="handleChange" :visible-item-count="5"></mt-select-picker>
  </div>
</template>

<style>
  @component-namespace page {
    @component datetime {
      @descendent wrapper {
        padding: 0 20px;
        position: absolute 50% * * *;
        width: 100%;
        transform: translateY(-50%);

        button:not:(last-child) {
          margin-bottom: 20px;
        }
      }
    }
  }
</style>

<script type="text/babel">
  import { Toast } from 'src/index';
  import MtSelectPicker from '../../packages/select-picker/src/select-picker';

  export default {
    components: {MtSelectPicker},
    data() {
      return {
        value: '1986',
        list: ['1984', '1985', '1986', '1987', '1988', '1989', '1990', '1991', '1992', '1993', '1994', '1995'],
        visible: false
      };
    },

    methods: {
      handleChange(v, picker) {
        Toast({
          message: '已选择 ' + v.toString(),
          position: 'bottom'
        });
      }
    }
  };
</script>
