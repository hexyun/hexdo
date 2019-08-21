<template>
    <mt-popup :visible.sync="visible" :position="position" class="mint-datetime">
        <mt-picker
            :slots="slots"
            @change="onChange"
            :visible-item-count="visibleItemCount"
            class="mint-datetime-picker"
            v-ref:picker
            show-toolbar>
            <span class="mint-datetime-action mint-datetime-cancel" @click="visible = false">{{ cancelText }}</span>
            <span class="mint-datetime-action mint-datetime-confirm" @click="confirm">{{ confirmText }}</span>
        </mt-picker>
    </mt-popup>
</template>

<style lang="css">
    @import "../../../src/style/var.css";

    @component-namespace mint {
        @component datetime {
            width:100%;
            .picker-slot-wrapper,
            .picker-item {
                backface-visibility: hidden;
            }

            .picker-toolbar {
                border-bottom: solid 1px #eaeaea;
                display: flex;
                justify-content: space-between;
                box-sizing: border-box;
            }

            @descendent action {
                line-height: 40px;
                font-size: 16px;
                color: $color-blue;
            }
            @descendent cancel {
                padding-left: 30px;
            }
            @descendent confirm {
                padding-right: 30px;
            }
        }
    }
</style>

<script type="text/babel">
  import picker from 'mint-ui/packages/picker/index.js';
  import popup from 'mint-ui/packages/popup/index.js';

  if(process.env.NODE_ENV === 'component') {
    require('mint-ui/packages/picker/style.css');
    require('mint-ui/packages/popup/style.css');
  }

  export default {
    name: 'mt-select-picker',
    props: {
      list: {
        default() {
          return []
        }, type: Array
      },
      visibleItemCount: {
        type: Number,
        default: 5
      },
      cancelText: {
        type: String,
        default: '取消'
      },
      confirmText: {
        type: String,
        default: '确认'
      },
      position: {
        type: String,
        default: 'bottom'
      },
      visible: {
        type: Boolean,
        default: false
      },
      key: {
        type: String,
        default: ''
      },
      value: {
        type: String,
        default: ''
      }
    },

    data() {
      return {
        currentIndex: this.getIndex(this.list, this.key, this.value),
        v: this.value
      }
    },
    computed: {
      slots() {
        return [{
          flex: 1,
          values: this.list,
          className: 'hex__select'
        }]
      }
    },

    components: {
      'mt-picker': picker,
      'mt-popup': popup
    },

    methods: {
      getIndex(list, k, v) {
        return list.findIndex(t => k ? t[k] == v : t == v)
      },
      onChange(picker, value) {
        this.v = value
        this.currentIndex = this.getIndex(this.list, this.key, value)
        this.$emit('change', value, this.currentIndex)
      },
      confirm() {
        this.visible = false
        this.$emit('confirm', this.v, this.currentIndex)
      },

      setSlotsByValues() {
        if(this.currentIndex === -1) return
        const setSlotValue = this.$refs.picker.setSlotValue;
        setSlotValue(0, this.slots[0].values[this.currentIndex]);
        [].forEach.call(this.$refs.picker.$children, child => child.doOnValueChange());
      },
    },

    watch: {
      visible(l, p) {
        if(l != p && l) this.setSlotsByValues()
      }
    },

    ready() {
    }
  };
</script>
