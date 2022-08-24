<script setup>
const props = defineProps({
    event: Object
})
const emit = defineEmits(['cancel', 'deleteEvent'])
</script>
<template>
    <div class="modal-add-event modal-show-event">
        <h3 class="text-center"><b>Event</b></h3>
        <p>{{ event.date }}</p>
        <p class="event-title">{{ event.title }}</p>
        <p>{{ event.description }}</p>
        <button class="btn mt-05" @click="emit('deleteEvent', event.title, event.date)">Delete</button>
        <span class="close-icon" @click="emit('cancel')">x</span>
    </div>
</template>