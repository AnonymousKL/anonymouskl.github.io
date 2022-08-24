<script setup>
import {onMounted} from 'vue';

let nav = 0;
const inputDate = {
    year: 2022,
    month: 8,
    day: 19
}
let events = [
    {
        date: '8/19/2022',
        title: 'This event title',
        content: 'Event content'
    },
    {
        date: '8/21/2022',
        title: 'This event title 2',
        content: 'Event content 2'
    },
    {
        date: '8/17/2022',
        title: 'This event title 3',
        content: 'Event content 3'
    },
    {
        date: '8/17/2022',
        title: 'This event title 3',
        content: 'Event content 3'
    }
]

const weekDaysEl = document.getElementById('weekdays');
const monthDaysEl = document.getElementById('monthdays');
const monthTitleEl = document.getElementById('month-title');
const modalEventEl = document.getElementById('modal-event');
const btnAddEvent = document.getElementById('btn-add-event');
const btnCancelEvent = document.getElementById('btn-cancel-event');

function generate() {
    const date = new Date(inputDate.year, inputDate.month - 1, inputDate.day); // TODO: receive input date

    if (nav !== 0) {
        date.setMonth(date.getMonth() + nav);
    }

    const day = date.getDate(); // 1 -> 31
    const month = date.getMonth(); // 0 -> 11
    const year = date.getFullYear();

    const monthTitle = date.toLocaleDateString('default', { month: 'long', year: 'numeric'});
    monthTitleEl.innerText = monthTitle;

    const currentDate = new Date();
    renderDateString('date-text', currentDate);

    const firstDayInMonth = new Date(year, month, 1);
    const daysInMonth = new Date(year, month + 1, 0).getDate(); // total days

    const firstDateString = firstDayInMonth.toLocaleDateString('en-us', {
        weekday: 'long',
        year: 'numeric',
        month: 'numeric',
        day: 'numeric',
    });
    const dateString = firstDateString.split(',')[0]; // Just get date text

    const spaceEmptyDay = weekDays.indexOf(dateString);

    monthDaysEl.innerHTML = '';
    for (let day = 1; day <= daysInMonth + spaceEmptyDay; day++) {
        let dayEl = document.createElement('div');

        if (day > spaceEmptyDay) {
            dayEl.classList.add('day');
            dayEl.innerHTML = `<span>${day - spaceEmptyDay}</span>`;

            // Highlight today
            if (
                currentDate.getFullYear() === year
                && currentDate.getMonth() === month
                && currentDate.getDate() === day
            ) {
                dayEl.classList.add('current-day');
            }

            // Render event tag
            const dayString = `${month + 1}/${day}/${year}`;
            renderEventTags(events, dayString, dayEl);

            dayEl.addEventListener('click', (e) => modalAddEvent(e, dayString));
        } else {
            dayEl.classList.add('empty-day')
        }

        monthDaysEl.appendChild(dayEl);
    }
}

function renderWeekDays() {
    weekDays.forEach(element => {
        const weekDaysItemEl = document.createElement('div');
        weekDaysItemEl.innerText = element;
        weekDaysEl.appendChild(weekDaysItemEl);
    });
}

function renderDateString(id, date) {
    const el = document.getElementById(id)
    el.innerText = date.toLocaleDateString('en-us', {
        weekday: 'long',
        year: 'numeric',
        month: 'numeric',
        day: 'numeric',
    });
}

function navigateMonth(state) {
    switch (state) {
        case 'prev':
            nav--
            generate()
            break;
        case 'next':
            nav++
            generate()
            break;
        case 'current':
        default:
            nav = 0
            generate();
    }
}

function renderEventTags(events, dateString, dayEl) {
    events.forEach((event) => {
        if (event.date === dateString) {
            const eventEl = document.createElement('p');
            eventEl.classList.add('event-tag')
            eventEl.innerText = event.title;
            dayEl.appendChild(eventEl);
        }
    });
}

function modalAddEvent(e, dayString) {
    if (!modalEventEl.classList.contains('show')) {
        modalEventEl.classList.add('show');
    }
    modalEventEl.style.top = e.pageY + 'px';
    modalEventEl.style.left = e.pageX + 'px';

    const eventTitleEl = document.getElementById('event-title');
    const eventDesEl = document.getElementById('event-description');
    eventTitleEl.value = '';
    eventDesEl.value = '';

    btnAddEvent.onclick = () => {
        let event = {
            date: dayString,
            title: eventTitleEl.value,
            content: eventDesEl.value,
        }
        events.push(event);
        generate(); // Re-render after add event
    };

    btnCancelEvent.addEventListener('click', () => {
        modalEventEl.classList.remove('show');
    })

}

onMounted(() => {
  renderWeekDays();
  generate();
  navigateMonth();
})

</script>

<template>
  <div class="container">
        <div class="header mb-1">
            <div class="left-content">
                <div class="mb-1">
                    <label for="date">Date</label>
                    <input name="date" id="date">
                </div>
                <div>
                    <button @click="navigateMonth('prev')" id="prev-month">Prev</button>
                    <button @click="navigateMonth('current')" id="current-month">Today</button>
                    <button @click="navigateMonth('next')" id="next-month">Next</button>
                </div>
            </div>
            <div class="center-content">
                <h3 id="month-title"></h3>
            </div>
            <div class="right-conten text-center">
                <p>Today</p>
                <p id="date-text"></p>
            </div>
        </div>
        <div id="weekdays" class="mb-1">
        </div>
        <div id="monthdays">
        </div>
    </div>
    <div class="modal-add-event" id="modal-event">
        <h3 class="text-center"><b>Add Event</b></h3>
        <input placeholder="Event title" id="event-title"/>
        <input placeholder="Description" id="event-description"/>
        <div class="d-flex justify-content-between">
            <button id="btn-cancel-event">Cancel</button>
            <button id="btn-add-event">Add</button>
        </div>
    </div>
</template>

