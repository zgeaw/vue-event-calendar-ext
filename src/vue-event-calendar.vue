<template>
  <div class="__vev_calendar-wrapper">
    <cal-panel
      :events="events"
      :calendar="calendarOptions"
      @cur-day-changed="handleChangeCurDay">
    </cal-panel>
    <cal-events
      :dayEvents="selectdDayEvents"
      :locale="calendarOptions.options.locale"
      :color="calendarOptions.options.color">
      <slot :showEvents="selectdDayEvents.events"></slot>
    </cal-events>
  </div>
</template>
<script>
import calEvents from './components/cal-events.vue'
import calPanel from './components/cal-panel.vue'

const inBrowser = typeof window !== 'undefined'
export default {
  name: 'vue-event-calendar',
  components: {
    'cal-events': calEvents,
    'cal-panel': calPanel
  },
  data () {
    return {
      selectdDayEvents: {
        date: 'all',
        events: this.events || []  //default show all event
      }
    }
  },
  props: {
    events: {
      type: Array,
      required: true
    }
  },
  computed: {
    calendarOptions () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA
      } else {
        return {
          options: {
            locale: 'en', //zh
            color: ' #f29543'
          },
          params: {
              curYear: dateObj.getFullYear(),
              curMonth: dateObj.getMonth(),
              curDate: dateObj.getDate(),
              curEvents: {
                title: 'all'
              }
          },
          events: []
        }
      }
    }
  },
  created () {
    if (this.calendarOptions.params.curEventsDate !== 'all') {
      this.handleChangeCurDay(this.calendarOptions.params.curEventsDate)
    }
  },
  methods: {
    handleChangeCurDay (date) {
      this.selectdDayEvents = {
        date: date,
        events: this.events.filter(function(event) {
          if (event.date === date) {
            return true
          } else {
            return false
          }
        })
      }
    }
  }
}
</script>
<style lang="less">
@base-orange: #f29543;
@white: #ffffff;
@gray: #e0e0e0;
@gray-dark: #b1b1b1;
@large-padding: 15px;
@small-padding: 10px;

@icon-border-size: 1px;
@media screen and (min-width: 768px) {
  .__vev_calendar-wrapper{
    max-width: 1200px;
    margin: 0 auto;
    .cal-wrapper{
      width: 50%;
      padding: 100px 50px;
      .date-num{
        line-height: 50px;
      }
    }
    .events-wrapper{
      width: 50%;
      background-color: @base-orange;
      color: @white;
      padding: 40px 50px;
      position: absolute;
      left: 50%;
      top: 0;
      bottom: 0;
    }
  }
}
@media screen and (max-width: 768px) {
  .__vev_calendar-wrapper{
    .cal-wrapper{
      width: 100%;
      padding: 10px 5px;
      .date-num{
        line-height: 42px;
      }
    }
    .events-wrapper{
      width: 100%;
      margin-top: 10px;
      padding: 10px;
    }
  }
}
.__vev_calendar-wrapper{
  position: relative;
  overflow: hidden;
  width: 100%;
  *{
    box-sizing: border-box;
  }
  .cal-wrapper{
    .cal-header{
      position: relative;
      width: 100%;
      background-color: @white;
      // box-shadow: 0 6px 5px rgba(0,0,0,.1);
      font-weight: 500;
      overflow: hidden;
      padding-bottom: 10px;
      &>div{
        float: left;
        line-height: 20px;
        padding: @large-padding;
      }
      .title{
        width: 60%;
        text-align: center;
      }
      .l{
        text-align: left;
        width: 20%;
        cursor: pointer;
        user-select: none;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
      }
      .r{
        text-align: right;
        width: 20%;
        cursor: pointer;
        user-select: none;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
      }
    }
    .cal-body{
      width: 100%;
      .weeks{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1rem;
        // border-right: 1px solid @gray;
        // border-left: 1px solid @gray;
        .item{
          line-height: 50px;
          float: left;
          width: 14.285%;
        }
      }
      .dates{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1rem;
        // border-right: 1px solid @gray;
        // border-bottom: 1px solid @gray;
        .item{
          position: relative;
          float: left;
          display: block;
          width: 14.285%;
          cursor: default;
          -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
          .date-num{
            font-size: 1rem;
          }
          &.event{
            color: @base-orange;
            cursor: pointer;
          }
          .is-event{
            content: '';
            border: 1px solid @base-orange;
            background-color: #fff;
            border-radius: 50%;
            width: 36px;
            height: 36px;
            position: absolute;
            left: 50%;
            top: 50%;
            z-index: -2;
            margin-left: -18px;
            margin-top: -19px;
          }
          &.today{
            color: @white;
          }
          .is-today{
            content: '';
            border: 1px solid @base-orange;
            background-color: @base-orange;
            border-radius: 50%;
            width: 36px;
            height: 36px;
            position: absolute;
            left: 50%;
            top: 50%;
            z-index: -1;
            margin-left: -18px;
            margin-top: -19px;
          }
        }
      }
    }
  }
  .events-wrapper{
    border-radius: 10px;
    .cal-events{
      height: 100%;
    }
    .date{
      width: 40%;
      min-width: 200px;
      text-align: center;
      color: @white;
      background-color: rgba(0, 0, 0, 0.2);
      border-radius: 20px;
      margin: 0 auto;
      font-size: 22px;
    }
    .event-item{
      padding: 5px 20px;
      margin-top: 15px;
      box-shadow: 0 3px 11px 2px rgba(0,0,0,.1);
      background-color: #fff;
      border-radius: 5px;
      color: #323232;
      position: relative;
      .title{
        height: 40px;
        line-height: 40px;
        color: #323232;
        font-size: 16px;
        border-bottom: 1px solid #f2f2f2;
      }
      .time{
        position: absolute;
        right: 30px;
        top: 17px;
        color: #9b9b9b;
        font-size: 14px;
      }
      .desc{
        color: #9b9b9b;
        font-size: 14px;
        padding: 7px 0;
      }
    }
  }
}

.arrow-left.icon {
  color: #000;
  position: absolute;
  left: 6%;
  margin-top: 10px;
}
.arrow-left.icon:before {
  content: '';
  position: absolute;
  left: 1px;
  top: -5px;
  width: 10px;
  height: 10px;
  border-top: solid @icon-border-size currentColor;
  border-right: solid @icon-border-size currentColor;
  -webkit-transform: rotate(-135deg);
          transform: rotate(-135deg);
}
.arrow-right.icon {
  color: #000;
  position: absolute;
  right: 6%;
  margin-top: 10px;
}
.arrow-right.icon:before {
  content: '';
  position: absolute;
  right: 1px;
  top: -5px;
  width: 10px;
  height: 10px;
  border-top: solid @icon-border-size currentColor;
  border-right: solid @icon-border-size currentColor;
  -webkit-transform: rotate(45deg);
          transform: rotate(45deg);
}
h3, p{
  margin: 0;
  padding: 0;
}
</style>