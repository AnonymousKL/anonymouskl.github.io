<script setup>
const props = defineProps({
    weekDays: Array
})
</script>

<template>
    <div id="weekdays" class="mb-1">
        <div v-for="(day, index) in weekDays" :key="index">
            {{day}}
        </div>
    </div>
</template>
