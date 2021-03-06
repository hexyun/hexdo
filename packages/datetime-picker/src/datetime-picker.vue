<template>
    <mt-popup :visible.sync="visible" position="bottom" class="mint-datetime">
        <mt-picker
            :slots="dateSlots"
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
            width:

        100%;
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
                color: $ color-blue;
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

  const FORMAT_MAP = {
    Y: 'year',
    M: 'month',
    D: 'date',
    H: 'hour',
    m: 'minute'
  };

  export default {
    name: 'mt-datetime-picker',

    props: {
      visible: {
        type: Boolean,
        default: false
      },
      cancelText: {
        type: String,
        default: '取消'
      },
      confirmText: {
        type: String,
        default: '确定'
      },
      format: {
        type: String,
        default: ''
      },
      type: {
        type: String,
        default: 'datetime'
      },
      startDate: {
        type: Date,
        default() {
          return new Date(new Date().getFullYear() - 2, 0, 1);
        }
      },
      endDate: {
        type: Date,
        default() {
          return new Date(new Date().getFullYear() + 2, 11, 31);
        }
      },
      startHour: {
        type: Number,
        default: 0
      },
      endHour: {
        type: Number,
        default: 23
      },
      yearFormat: {
        type: String,
        default: '{value}'
      },
      monthFormat: {
        type: String,
        default: '{value}'
      },
      dateFormat: {
        type: String,
        default: '{value}'
      },
      hourFormat: {
        type: String,
        default: '{value}'
      },
      minuteFormat: {
        type: String,
        default: '{value}'
      },
      visibleItemCount: {
        type: Number,
        default: 5
      },
      value: null
    },

    data() {
      return {
        startYear: null,
        endYear: null,
        startMonth: 1,
        endMonth: 12,
        startDay: 1,
        endDay: 31,
        selfTriggered: false,
        isSlotChange: false,
        dateSlots: [],
        shortMonthDates: [],
        longMonthDates: [],
        febDates: [],
        leapFebDates: []
      };
    },

    components: {
      'mt-picker': picker,
      'mt-popup': popup
    },

    methods: {
      isLeapYear(year) {
        return (year % 400 === 0) || (year % 100 !== 0 && year % 4 === 0);
      },

      isShortMonth(month) {
        return [4, 6, 9, 11].indexOf(month) > -1;
      },

      getMonthEndDay(year, month) {
        if(this.isShortMonth(month)) {
          return 30;
        } else if(month === 2) {
          return this.isLeapYear(year) ? 29 : 28;
        } else {
          return 31;
        }
      },

      getTrueValue(formattedValue) {
        if(!formattedValue) return;
        while(isNaN(parseInt(formattedValue, 10))) {
          formattedValue = formattedValue.slice(1);
        }
        return parseInt(formattedValue, 10);
      },

      getValue(values) {
        let value;
        if(this.type === 'time') {
          value = values.map(value => ('0' + this.getTrueValue(value)).slice(-2)).join(':');
        } else {
          let year = this.getTrueValue(values[0]);
          let month = this.getTrueValue(values[1]);
          let date = this.getTrueValue(values[2]);
          let hour, minute

          if(this.type === 'datetime' && this.format) {
            [year, month, date] = values[0].split(this.splitor).map(_ => this.getTrueValue(_))
          }
          let maxDate = this.getMonthEndDay(year, month);
          if(date > maxDate) {
            this.selfTriggered = true;
            date = 1;
          }
          if(this.type === 'datetime' && this.format) {
            hour = this.getTrueValue(values[1] || 0)
            minute = this.getTrueValue(values[2] || 0)
          } else {
            hour = this.typeStr.indexOf('H') > -1 ? this.getTrueValue(values[this.typeStr.indexOf('H')]) : 0;
            minute = this.typeStr.indexOf('m') > -1 ? this.getTrueValue(values[this.typeStr.indexOf('m')]) : 0;
          }
          value = new Date(year, month - 1, date, hour, minute);
        }
        return value;
      },

      onChange(picker) {
        let values = picker.$children.filter(child => child.value !== undefined).map(child => child.value);
        if(this.selfTriggered) {
          this.selfTriggered = false;
          return;
        }
        this.value = this.getValue(values);
      },

      fillValues(type, start, end) {
        let values = [];
        for(let i = start; i <= end; i++) {
          if(i < 10) {
            values.push(this[`${FORMAT_MAP[type]}Format`].replace('{value}', ('0' + i).slice(-2)));
          } else {
            values.push(this[`${FORMAT_MAP[type]}Format`].replace('{value}', i));
          }
        }
        return values;
      },

      pushSlots(slots, type, start, end) {
        slots.push({
          flex: 1,
          values: this.fillValues(type, start, end)
        });
      },

      generateSlots() {
        let dateSlots = [];
        const INTERVAL_MAP = {
          Y: this.rims.year,
          M: this.rims.month,
          D: this.rims.date,
          H: this.rims.hour,
          m: this.rims.min
        };
        let typesArr = this.typeStr.split('');
        if(this.format && this.type === 'datetime') {
          typesArr.splice(0, this.format.length)
          this.formatSlots(dateSlots, this.format)
        }
        typesArr.forEach(type => {
          if(INTERVAL_MAP[type]) {
            this.pushSlots.apply(null, [dateSlots, type].concat(INTERVAL_MAP[type]));
          }
        });

        if(this.typeStr === 'Hm') {
          dateSlots.splice(1, 0, {
            divider: true,
            content: ':'
          });
        }
        if(this.type === 'datefield') {
          dateSlots.push({
            flex: 1,
            values: ['上午', '下午']
          })
        }
        this.dateSlots = dateSlots;
        this.handleExceededValue();
      },

      formatSlots(dateSlots, format) {
        let values = []
        const startDate = this.startDate
        const endDate = this.endDate
        const splitor = this.splitor

        function _zero(n) {
          if(n < 10) return `0${n}`
          return n
        }

        const unit = 1000 * 60 * 60 * 24
        const sTime = +startDate
        const d = Math.floor((+endDate - sTime) / unit)
        const sY = startDate.getFullYear()
        const _x = function(d) {
          const y = d.getFullYear()
          const m = d.getMonth()
          d = d.getDate()
          return `${y}${splitor}${_zero(m + 1)}${splitor}${_zero(d)}`
        }

        for(let i = 0; i <= d; i++) {
          values.push(_x(new Date(sTime + unit * i)))
        }
        dateSlots.push({
          flex: 1,
          values
        })
      },

      handleExceededValue() {
        let values = [];
        if(this.type === 'time') {
          values = this.value.split(':');
        } else {
          values = [
            this.yearFormat.replace('{value}', this.getYear(this.value)),
            this.monthFormat.replace('{value}', ('0' + this.getMonth(this.value)).slice(-2)),
            this.dateFormat.replace('{value}', ('0' + this.getDate(this.value)).slice(-2))
          ];
          if(this.type === 'datetime' && this.format) {
            values = [values.join(this.splitor)]
          }
          if(this.type === 'datetime') {
            values.push(
              this.hourFormat.replace('{value}', ('0' + this.getHour(this.value)).slice(-2)),
              this.minuteFormat.replace('{value}', ('0' + this.getMinute(this.value)).slice(-2))
            );
          }
        }
        this.dateSlots.filter(child => child.values !== undefined).map(slot => slot.values).forEach((slotValues, index) => {
          if(slotValues.indexOf(values[index]) === -1) {
            values[index] = slotValues[0];
          }
        });
        this.$nextTick(() => {
          this.setSlotsByValues(values);
        });
      },

      setSlotsByValues(values) {
        const setSlotValue = this.$refs.picker.setSlotValue;
        if(this.type === 'time') {
          setSlotValue(0, values[0]);
          setSlotValue(1, values[1]);
        }
        if(this.type !== 'time') {
          setSlotValue(0, values[0]);
          setSlotValue(1, values[1]);
          setSlotValue(2, values[2]);
          if(this.type === 'datetime') {
            setSlotValue(3, values[3]);
            setSlotValue(4, values[4]);
          }
        }
        [].forEach.call(this.$refs.picker.$children, child => child.doOnValueChange());
      },

      rimDetect(result, rim) {
        let position = rim === 'start' ? 0 : 1;
        let rimDate = rim === 'start' ? this.startDate : this.endDate;
        if(this.getYear(this.value) === rimDate.getFullYear()) {
          result.month[position] = rimDate.getMonth() + 1;
          if(this.getMonth(this.value) === rimDate.getMonth() + 1) {
            result.date[position] = rimDate.getDate();
            if(this.getDate(this.value) === rimDate.getDate()) {
              result.hour[position] = rimDate.getHours();
              if(this.getHour(this.value) === rimDate.getHours()) {
                result.min[position] = rimDate.getMinutes();
              }
            }
          }
        }
      },

      isDateString(str) {
        return /\d{4}(\-|\/|.)\d{1,2}\1\d{1,2}/.test(str);
      },

      getYear(value) {
        return this.isDateString(value) ? value.split(' ')[0].split(/-|\/|\./)[0] : value.getFullYear();
      },

      getMonth(value) {
        return this.isDateString(value) ? value.split(' ')[0].split(/-|\/|\./)[1] : value.getMonth() + 1;
      },

      getDate(value) {
        return this.isDateString(value) ? value.split(' ')[0].split(/-|\/|\./)[2] : value.getDate();
      },

      getHour(value) {
        if(this.isDateString(value)) {
          const str = value.split(' ')[1] || '00:00:00';
          return str.split(':')[0];
        }
        return value.getHours();
      },

      getMinute(value) {
        if(this.isDateString(value)) {
          const str = value.split(' ')[1] || '00:00:00';
          return str.split(':')[1];
        }
        return value.getMinutes();
      },

      confirm() {
        this.visible = false;
        this.$emit('confirm', this.value);
      }
    },

    computed: {
      rims() {
        if(!this.value) return {year: [], month: [], date: [], hour: [], min: []};
        let result;
        if(this.type === 'time') {
          result = {
            hour: [this.startHour, this.endHour],
            min: [0, 59]
          };
          return result;
        }
        result = {
          year: [this.startDate.getFullYear(), this.endDate.getFullYear()],
          month: [1, 12],
          date: [1, this.getMonthEndDay(this.getYear(this.value), this.getMonth(this.value))],
          hour: [0, 23],
          min: [0, 59]
        };
        this.rimDetect(result, 'start');
        this.rimDetect(result, 'end');
        return result;
      },

      typeStr() {
        if(this.type === 'time') {
          return 'Hm';
        } else if(['date', 'datefield'].includes(this.type)) {
          return 'YMD';
        } else if(this.format && this.type === 'datetime') {
          return this.format.toUpperCase() + 'Hm'
        } else {
          return 'YMDHm';
        }
      }
    },

    watch: {
      value() {
        this.handleExceededValue();
      },
      rims(val, oldVal) {
        let same = Object.keys(val).every(key => val[key][0] === oldVal[key][0] &&
          val[key][1] === oldVal[key][1]);
        if(!same) {
          this.generateSlots();
        }
      }
    },

    ready() {
      if(this.format) {
        const format = this.format
        const [Y, ,] = format.split(/[^ymd]/ig)
        this.splitor = format[format.indexOf(Y) + 1]
      }

      if(!this.value) {
        if(this.type.indexOf('date') > -1) {
          this.value = this.startDate;
        } else {
          this.value = `${ ('0' + this.startHour).slice(-2) }:00`;
        }
      }
      this.generateSlots();
    }
  };
</script>
