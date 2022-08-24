<script setup>
import { reactive, ref } from 'vue';
import AddEvent from './modal/AddEvent.vue';
import ShowEvent from './modal/ShowEvent.vue';

const props = defineProps({
    days: Array,
})
const state = reactive({
    showModel: false,
    showEventModel: false,
    eventDetail: {},
    modelPosition: {},
    dayString: ''
})
const emit = defineEmits(['active', 'addedEvent', 'deleteEvent'])

function activeDay(e, day) {
    emit('active', day)
    state.showModel = true
    state.showEventModel = false

    state.modelPosition.top = e.y + 'px'
    state.modelPosition.left = e.x + 'px'
    state.dayString = day.dayString
}
function closeModel() {
    state.showModel = false
    state.showEventModel = false
}
function addedEvent(data) {
    emit('addedEvent', data)
    state.showModel = false
}
function showEvent(e, event) {
    state.eventDetail = event
    state.showEventModel = true
    state.showModel = false

    state.modelPosition.top = e.y + 'px'
    state.modelPosition.left = e.x + 'px'
}
function onDeleteEvent(eventTitle, eventDate) {
    emit('deleteEvent', eventTitle, eventDate)
    closeModel()
}
</script>

<template>
    <template v-for="day in days">
        <div v-if="!day.isEmpty" class="day" @click.self="(e) => activeDay(e, day)"
            :class="day.isCurrent ? 'current-day' : ''">
            <span>{{ day.text }}</span>
            <template v-if="day.event">
                <p v-for="event in day.event" @click="(e) => showEvent(e, event)"
                class="event-tag">
                    {{event.title}}
                </p>
            </template>
        </div>
        <div v-else="day.isEmpty" class="empty-day">
        </div>
    </template>
    <AddEvent v-if="state.showModel" :style="state.modelPosition" :dayString="state.dayString"
    @keyup.esc="closeModel"
        class="show" @cancel="closeModel" @addEvent="addedEvent" />
    <ShowEvent v-if="state.showEventModel" :style="state.modelPosition" 
        class="show" @cancel="closeModel" :event="state.eventDetail" @delete-event="onDeleteEvent"/>
</template>