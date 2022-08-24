<script setup>
import Weekdays from './components/Weekdays.vue'
import Navigation from './components/Navigation.vue';
import Day from './components/Day.vue';
import { ref, reactive, watch } from 'vue';

const nav = ref(0)
const daysInMonth = ref(null)
const monthTitle = ref('')
const dayArr = ref([])
const currentDateText = ref('')

const eventInStorage = localStorage.getItem('events') ? JSON.parse(localStorage.getItem('events')) : []

const state = reactive({
  events: eventInStorage,
  inputDate: {},
  activeYear: null
})

const weekDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'];
load(state.events)
watch([nav, state.events, state.inputDate], () => {
  load(state.events);
})
function load(events) {
  const date = new Date()

  if (state.inputDate.year != undefined) {
    date.setFullYear(state.inputDate.year)
  }
  if (state.inputDate.month != undefined) {
    date.setMonth(state.inputDate.month)
  }
  if (nav.value !== 0) {
    date.setMonth(date.getMonth() + nav.value);
  }

  const day = date.getDate(); // 1 -> 31
  const month = date.getMonth(); // 0 -> 11
  const year = date.getFullYear();

  monthTitle.value = date.toLocaleDateString('default', { month: 'long', year: 'numeric' });

  const currentDate = new Date()
  currentDateText.value = currentDate.toLocaleDateString('en-us', {
    weekday: 'long',
    year: 'numeric',
    month: 'numeric',
    day: 'numeric',
  });

  const firstDayInMonth = new Date(year, month, 1);
  daysInMonth.value = new Date(year, month + 1, 0).getDate(); // total days

  const firstDateString = firstDayInMonth.toLocaleDateString('en-us', {
    weekday: 'long',
    year: 'numeric',
    month: 'numeric',
    day: 'numeric',
  });
  const dateString = firstDateString.split(',')[0]; // Just get date text

  const spaceEmptyDay = weekDays.indexOf(dateString);

  dayArr.value = []
  for (let day = 1; day <= daysInMonth.value + spaceEmptyDay; day++) {
    let dayObj = {}
    if (day > spaceEmptyDay) {
      dayObj = {
        text: (day - spaceEmptyDay),
        isEmpty: false
      }
      // Highlight today
      if (
        currentDate.getFullYear() === year
        && currentDate.getMonth() === month
        && currentDate.getDate() === day
      ) {
        dayObj.isCurrent = true
      }
      // Render event tag
      const dayString = `${month + 1}/${day}/${year}`;
      const event = events.filter(e => e.date == dayString)
      dayObj.event = event
      dayObj.dayString = dayString
    } else {
      dayObj.isEmpty = true
    }
    dayArr.value.push(dayObj)
  }

}

function updateDate(navigate) {
  switch (navigate) {
    case 'prev':
      nav.value--
      break;
    case 'next':
      nav.value++
      break;
    case 'current':
    default:
      nav.value = 0
      state.inputDate.month = undefined
      state.inputDate.year = undefined
  }
}
function reloadEvents(event) {
  state.events.push(event)
  localStorage.setItem('events', JSON.stringify(state.events))
}
function onChangeMonth(month) {
  nav.value = 0
  state.inputDate.month = month
}
function onChangeYear(year) {
  state.inputDate.year = year
}
function deleteEvent(eventTitle, eventDate) {
  state.events.forEach((event, index) => {
    if (event.title === eventTitle && event.date === eventDate) {
      state.events.splice(index, 1)
    }
  })
}
</script>

<template>
  <div class="container">
    <Navigation 
      :month-title="monthTitle" :date-title="currentDateText" 
      @navigate-month="updateDate" 
      @change-month="onChangeMonth"
      @change-year="onChangeYear"
    />
    <Weekdays :week-days="weekDays" />
    <div id="monthdays">
      <Day :days="dayArr" @added-event="reloadEvents" @delete-event="deleteEvent"/>
    </div>
  </div>
</template>