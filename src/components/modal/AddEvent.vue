<script setup>
import { ref } from 'vue';
const props = defineProps({
    dayString: String,
})

const emit = defineEmits(['cancel, addEvent'])
const eventTitle = ref('')
const eventDes = ref('')
function addEvent() {
    emit('addEvent', {
        title: eventTitle.value,
        description: eventDes.value,
        date: props.dayString
    })
}
function closeModal() {
    emit('cancel')
}
</script>

<template>
    <div class="modal-add-event" id="modal-event" ref="modal">
        <h3 class="text-center"><b>Add Event</b></h3>
        <p>{{dayString}}</p>
        <input placeholder="Event title" v-model="eventTitle"/>
        <input placeholder="Description" v-model="eventDes"/>
        <div class="d-flex justify-content-between">
            <button class="btn" @click="closeModal()">Cancel</button>
            <button class="btn" @click="addEvent()">Add</button>
        </div>
        <span class="close-icon" @click="closeModal()">x</span>
    </div>
</template>