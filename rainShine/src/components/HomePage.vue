<script setup>
import { defineComponent, ref, computed, provide } from 'vue';
import DayCard from './DayCard.vue';
import TodaysForecast from './TodaysForecast.vue';
import ForecastLocation from './ForecastLocation.vue';
import ForecastMethod from './ForecastMethod.vue';

defineComponent({ DayCard, TodaysForecast, ForecastLocation, ForecastMethod });
let forecastInfo = ref({});

const updateForecast = (update) => forecastInfo.value = update;


let start = ref(1);
let limit = computed(() => start.value + 6);
const currDays = computed(() => {
    try {
        const { days } = forecastInfo.value;
        return days.slice(start.value, limit.value);
    } catch (error) {
        console.log("not yet loaded:", error.message);
        return error;
    }
});



const methodChoice = (methodChoice) => method.value = methodChoice;

const method = ref('C');
const weatherMethod = {
    F: (deg) => `${Math.round(parseInt((deg * 9 / 5) + 32))}° F`,
    C: (deg) => `${deg}° C`
};

const provideMethod = computed(() => weatherMethod[method.value]);
provide('methodUpdate', provideMethod);
</script>

<template>
    <div class="location-container methods-container">
        <forecast-location @forecast-update="updateForecast" class="location-container" />
        <forecast-method @method-choice="methodChoice" />
    </div>

    <div class="main-weather-container">
        <div class="today-card">
            <todays-forecast :forecast-info="forecastInfo" />
        </div>
        <div class="day-container">
            <div class="more left" v-if="start > 1" @click="start--"><i class='bx bxs-left-arrow'></i></div>
            <DayCard v-for="(day, i) in currDays" :key="i" :forecast-info="day" />
            <div class="more right" v-if="start < currDays.length" @click="start++"><i class='bx bxs-right-arrow'></i></div>
        </div>

    </div>
</template>

<style scoped>
h1 {
    font-weight: 500;
    font-size: 2.6rem;
    position: relative;
    top: -10px;
}

h3 {
    font-size: 1.2rem;
}



.methods-container {
    display: flex;
    margin-bottom: 20px;
    top: 10px;
    column-gap: 8px;
}

.main-weather-container {
    margin-top: 70px;
    background: rgb(239, 239, 239);
    width: 93vw;
    border-top-left-radius: 40px;
    border-top-right-radius: 40px;
}

.day-container {
    display: flex;
    justify-content: space-between;
    gap: 8px;
    transition: all 0.3s ease;
}

.location-container {
    position: absolute;
    top: 2;
    right: 5px;
}


.more {
    height: 240px;
    width: 37px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    background: #00000017;
    transition: all ease 0.3s;
}

/* .more .right{
    position: absolute;
    right: 0;
}

.more .left{
    position: absolute;
    left:0;
    background: red;
} */

.more:hover {
    background: #000000a4;
}

@media (min-width: 1024px) {

    .greetings h1,
    .greetings h3 {
        text-align: left;
    }
}
</style>
