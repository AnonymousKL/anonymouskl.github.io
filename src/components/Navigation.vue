<script setup>
import { ref } from 'vue';

const props = defineProps({
    monthTitle: String,
    dateTitle: String,
    selectedMonth: Number,
    selectedYear: Number
})
const monthSelect = ref()
const yearSelect = ref()
const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
const emit = defineEmits(['navigateMonth', 'changeMonth', 'changeYear'])
const years = {
    range: 20,
    start: 2010,
}
</script>

<template>
<div class="header mb-3">
    <div class="left-content">
        <div class="mb-1">
            <label for="month">Month</label>
            <select name="month" v-model="monthSelect" @change="emit('changeMonth', monthSelect)">
                <option v-for="(month, index) in months" :value="index">
                    {{month}}
                </option>
            </select>
            <label for="year" class="ml-1">Year</label>
            <select name="year" class="year-select" v-model="yearSelect" @change="emit('changeYear', yearSelect)">
                <option v-for="year in years.range" :value="year + years.start">
                    {{year + years.start}}
                </option>
            </select>
        </div>
        <div class="nav-btn">
            <button class="btn" @click="emit('navigateMonth', 'prev')" id="prev-month">Prev</button>
            <button class="btn" @click="emit('navigateMonth', 'current')" id="current-month">Today</button>
            <button class="btn" @click="emit('navigateMonth', 'next')" id="next-month">Next</button>
        </div>
    </div>
    <div class="center-content">
        <h3 id="month-title">{{monthTitle}}</h3>
    </div>
    <div class="right-conten text-center">
        <p>Today</p>
        <p id="date-text">{{dateTitle}}</p>
    </div>
</div>
</template>