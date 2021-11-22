<template>
  <div id="app">
    <calendar :events='events' />
  </div>
</template>

<script>
import Calendar from '@/components/Calendar';
import {RED_TYPE, YELLOW_TYPE, GREEN_TYPE} from '@/utils/taskTypes';
// const mask = 'd YYYY-MM-DD';
// const daysPerWeek = 7;
// const weekDays = ["понедельник", "вторник", "среда", "четверг", "пятница", "суббота", "воскресенье"]

export default {
  name: 'App',
  data() {
    return {
      events: [
        {
          id: 1,
          date: "2021-11-21T18:31:42",
          type: GREEN_TYPE,
          name: "Сдать тестовое задание",
        },
        {
          id: 2,
          date: "2021-12-22T16:00:42",
          type: YELLOW_TYPE,
          name: "Позавтракать",
        },
        {
          id: 3,
          date: "2021-11-22T17:05:42",
          type: YELLOW_TYPE,
          name: "Залить тестовое на гит",
        },
        {
          id: 4,
          date: "2021-11-22T14:30:42",
          type: RED_TYPE,
          name: "Пройти собеседование очень длинный текст, чтобы было видно, что раскрылся весь бэйдж",
        },
        {
          id: 5,
          date: "2021-11-23T16:15:42",
          type: RED_TYPE,
          name: "Поспать",
        },
      ],
    }
  },
  components: {
    "calendar": Calendar
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600;700&display=swap');

body {
  margin: 0;
  font-family: Montserrat, sans-serif;
  font-weight: 400;
}

* {
  box-sizing: border-box;
}

#app {
  background-color: whitesmoke;
  padding: 1rem
}
</style>
