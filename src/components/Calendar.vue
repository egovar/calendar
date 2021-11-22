<template>
  <div class='calendar'>
    <div class='calendar__tools'>
      <button
          @click='prevMonth'
          class='calendar__button calendar__button_backwards'
      >
        &#60;
      </button>
      <h2 class='calendar__current-date'>
        {{ displayingMonthName }}
      </h2>
      <button
          @click='nextMonth'
          class='calendar__button calendar__button_backwards'
      >
        &#62;
      </button>

    </div>
    <div class='calendar__grid'>
      <div
          v-for='day in weekDays'
          class='calendar__header'
          :key='day'
      >
        {{ day }}
      </div>
      <div
          v-for='date in visibleDates'
          :key="date.iso"
          class='calendar__cell'
          :class='getCellClass(date.iso)'
      >
        <div class='calendar__date'>{{ date.day }}</div>
        <div class='calendar__events'>
          <div
              v-for='event in getCurrentDayEvents(date.iso)'
              :key='event.id'
              class='calendar__event-badge'
              :class='getEventClass(event.type)'
          >
            {{ getEventTime(event.date) }} {{ event.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import {weekDays, daysPerWeek, dateMask} from '@/utils/globals'
import {RED_TYPE, YELLOW_TYPE, GREEN_TYPE} from '@/utils/taskTypes'

moment.locale('ru')


export default {
  name: 'Calendar',
  props: {
    events: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      displayingMonth: moment().date(1),
      weekDays
    }
  },
  computed: {
    visibleDates() {
      const dates = []
      const displayingMonthDaysAmount = this.displayingMonth.daysInMonth()
      const displayingMonthFirstDayOfWeek = moment(this.displayingMonth).date(1).day() - 1
      const nextMonthVisibleDaysAmount = (daysPerWeek - ((displayingMonthDaysAmount + displayingMonthFirstDayOfWeek) % daysPerWeek)) % daysPerWeek
      const visibleDaysAmount = displayingMonthFirstDayOfWeek + displayingMonthDaysAmount + nextMonthVisibleDaysAmount
      const firstVisibleDay = moment(this.displayingMonth).date(1).subtract(displayingMonthFirstDayOfWeek, 'd')
      for (let i = 0; i < visibleDaysAmount; i++) {
        const date = moment(firstVisibleDay).add(i, 'd')
        dates.push({
          iso: date.format(dateMask),
          day: date.date()
        })
      }
      return dates
    },
    displayingMonthName() {
      if (moment().format('YYYY') !== this.displayingMonth.format('YYYY')) {
        return this.displayingMonth.format('MMMM YYYY')
      }
      return this.displayingMonth.format('MMMM')
    }
  },
  methods: {
    getCellClass(date) {
      const base = 'calendar__cell'
      const today = moment().format(dateMask)
      if (moment(today).isAfter(moment(date))) return base + '_earlier'
      if (moment(today).isSame(moment(date))) return base + '_today'
    },
    getEventClass(eventType) {
      const base = 'calendar__event-badge'
      if (eventType === GREEN_TYPE) return base + '_green'
      if (eventType === YELLOW_TYPE) return base + '_yellow'
      if (eventType === RED_TYPE) return base + '_red'
    },
    getCurrentDayEvents(date) {
      return this.events.filter((event) => moment(date).isSame(moment(event.date), 'd'))
          .sort((event) => moment(date).format("X") - moment(event.date).format("X"))
    },
    getEventTime(date) {
      return moment(date).format('HH:mm')
    },
    prevMonth() {
      this.displayingMonth = moment(this.displayingMonth.subtract(1, 'M'))
    },
    nextMonth() {
      this.displayingMonth = moment(this.displayingMonth.add(1, 'M'))
    }
  }
}
</script>

<style scoped>

.calendar {
  border-radius: 1rem;
  background-color: white;
  padding: 1rem;
}

.calendar__tools {
  display: flex;
  align-items: center;
}

.calendar__current-date {
  font-weight: 700;
  margin: 0 0.5rem;
  color: darkslateblue;
}

.calendar__header {
  text-align: right;
  font-weight: 600;
  text-transform: uppercase;
  padding: 0.25rem 0;
}

.calendar__header:nth-child(6),
.calendar__header:nth-child(7) {
  color: gray;
}

.calendar__grid {
  display: grid;
  grid-template-columns: repeat(7, calc(100% / 7 - 0.1rem * 6 / 7)); /* subtract gaps */
  gap: 0.1rem;
}

.calendar__cell {
  border-radius: 0.25rem;
  border: 0.25rem solid whitesmoke;
  height: 8rem;
  padding: 0.25rem;
  z-index: 1;
  display: flex;
  flex-direction: column;
}

.calendar__cell_today {
  border-color: gray;
}

.calendar__cell_today > .calendar__date {
  color: lightgreen;
}

.calendar__cell_earlier {
  background-color: whitesmoke;
}

.calendar__cell_earlier > .calendar__date {
  color: gray;
}

.calendar__cell:nth-child(7n):not(.calendar__cell_today) > .calendar__date,
.calendar__cell:nth-child(7n - 1):not(.calendar__cell_today) > .calendar__date {
  color: mediumpurple;
}

.calendar__date, .calendar__event-badge:not(:last-child) {
  margin-bottom: 0.25rem;
}

.calendar__date {
  font-weight: 600;
  text-align: right;
}

.calendar__events {
  overflow-y: auto;
}

.calendar__event-badge {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  width: fit-content;
  max-width: 100%;
  padding: 0.2rem;
  border-radius: 0.25rem;
  background-color: red;
  cursor: pointer;
  line-height: 1.5rem;
  z-index: 3;
}

.calendar__event-badge:hover {
  word-break: break-word;
  white-space: unset;
}

.calendar__event-badge_green {
  background-color: rgba(0, 255, 0, 0.3);
  color: darkgreen;
}

.calendar__event-badge_yellow {
  background-color: rgba(255, 255, 0, 0.3);
  color: darkgoldenrod;
}

.calendar__event-badge_red {
  background-color: rgba(255, 0, 0, 0.3);
  color: darkred;
}

.calendar__button {
  font-weight: 700;
  color: gray;
  background: none;
  border: none;
  cursor: pointer;
}
</style>