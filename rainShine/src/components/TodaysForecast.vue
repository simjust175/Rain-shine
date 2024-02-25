<template>
    <div class="today-card-container" :class="timeOfDay">
        <div class="main-weather">
            <div class="temp-info">
                <p id="location"><i class='bx bxs-map'></i>{{ forecastInfo.timezone?.split("/").pop() }}</p>
                <h1 v-if="forecastInfo.currentConditions" id="temp-container">
                    {{ weatherMethod(forecastInfo.currentConditions.temp) }}
                    <!-- <span style="font-size: 0.7em; vertical-align: super;">{{ method.value === 'F' ? 'F°' : 'C°' }}</span> -->
                </h1>
                <div class="loading-container" v-else>
                    <div class="loading-spinner"></div>
                </div>
                <div v-if="forecastInfo.currentConditions">
                    <p>{{ forecastInfo.currentConditions.conditions }}</p>
                </div>
            </div>
            <div class="greeting">
                <h3> Good {{ greeting }}</h3>
            </div>
            <img :src="`/weather_icons/${forecastInfo.currentConditions.icon}.svg`"
                :alt="forecastInfo.currentConditions.icon" class="today-img" :class="forecastInfo.currentConditions.icon"
                v-if="forecastInfo.currentConditions">


        </div>

        <div class="more-info">
            <div>
                <p v-show="hovered" @mouseenter="hovered = !hovered">Hi of </p><i class='bx bxs-up-arrow hi'></i>
                <p v-if="forecastInfoDay1">{{ weatherMethod(forecastInfoDay1.tempmax).slice(0, -2) }}</p>
                <div class="loading-spinner small-spinners" v-else></div>
            </div>
            <div>
                <p v-show="hovered" @mouseenter="hovered = !hovered">Low of</p> <i class='bx bxs-down-arrow low'></i>
                <p v-if="forecastInfoDay1">{{ weatherMethod(forecastInfoDay1.tempmin).slice(0, -2) }}</p>
                <div class="loading-spinner small-spinners" v-else></div>
            </div>
            <div>
                <p v-show="hovered" @mouseenter="hovered = !hovered">Wind-speed</p> <i class='bx bx-wind'></i>
                <p v-if="forecastInfoDay1">{{ forecastInfoDay1.windspeed }}<span class="more-info-method">Mph</span></p>
                <div class="loading-spinner small-spinners" v-else></div>
            </div>
            <div>
                <p v-show="hovered" @mouseenter="hovered = !hovered">Feels like</p><i class='bx bxs-thermometer'></i>
                <p v-if="forecastInfoDay1">{{ weatherMethod(forecastInfoDay1.feelslike).slice(0, -2) }}</p>
                <div class="loading-spinner small-spinners" v-else></div>
            </div>
            <div>
                <p v-show="hovered" @mouseenter="hovered = !hovered">Humidity</p> <i class="bx bxs-droplet"></i>
                <p v-if="forecastInfo.currentConditions">{{ forecastInfo.currentConditions.humidity }}%</p>
                <div class="loading-spinner small-spinners" v-else></div>
            </div>
            <div v-if="forecastInfo.snowdepth > 0">
                <p v-show="hovered" @mouseenter="hovered = !hovered">snow depth</p><i class='bx bxs-snowflake'></i>
                <p>{{ forecastInfoDay1.snowdepth }}Inch ?</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, defineProps, computed, watch, inject } from 'vue';
const props = defineProps({ forecastInfo: Object });


let providedMethod = inject('methodUpdate');
const weatherMethod = computed(() => providedMethod.value);

const state = ref({
    time: new Date().getHours(),
    //time: props.forecastInfo.currentCondition ? props.forecastInfo.currentCondition.datetime.split(":") : null,
    hovered: false,
});


const forecastInfoDay1 = ref({});
watch(() => props.forecastInfo, (update) => {
    forecastInfoDay1.value = update.days[0];
}, {
    deep: true
});

const timeOfDay = computed(() => state.value.time + props.forecastInfo.tzoffset > 6 && state.value.time + props.forecastInfo.tzoffset < 20 ? 'day' : 'night2');

const greeting = computed(() => {
    try {
        const timeZoneOffset = props.forecastInfo.tzoffset;
        console.log("offset", timeZoneOffset)
    } catch (error) {
        console.error(error.nessage);
    }
    const time = parseInt(state.value.time) + props.forecastInfo.tzoffset;
    return time >= 21 || time < 5 ? 'Night' :
        time >= 18 ? 'Evening' :
            time >= 12 ? 'Afternoon' :
                time >= 5 ? 'Morning' :
                    '?... not really: wrong time format';

});

</script>

<style>
.loading-spinner {
    margin-top: 50px;
    margin-left: 40px;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    border: 4px solid #f3f3f3;
    border-top: 4px solid #3498db;
    animation: spin 2s linear infinite;
}

.small-spinners {
    width: 15px;
    height: 15px;
    border: 2px solid #f3f3f3;
    border-top: 2px solid #3498db;
    margin: 0;
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

.today-card-container {
    position: relative;
    height: 253px;
    width: 1200px;
    margin-bottom: 20px;
    background: url(/backgrounds/night2.svg);
    background-position: center;
    background-size: cover;
    background-repeat: no-repeat;
    border-radius: 15px;
    color: aliceblue;
    box-shadow: 2px 2px 2px #bcbcbc;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    font-size: 40px;
    text-shadow: #bcbcbc 2px 2px 2px 2px;
    overflow: hidden;
}

#temp-container{
    margin-top: 20px;
}

.day {
    background: url(/backgrounds/day.svg);
    background-position: center;
    background-size: cover;
    background-repeat: no-repeat;
    color: #00187a;
}

.greeting {
    position: absolute;
    top: 8px;
    left: 37%;
    color: #cccdcf;
    text-shadow: 1px 1px 10px #767676;
}

.temp-info {
    padding-left: 30px;
}

#location {
    font-size: 20px;
    position: absolute;
    top: 10px;
}

.more-info {
    display: flex;
    padding: 0 10px 0 50px;
}

.more-info div {
    font-size: 15px;
    margin-left: 20px;
    white-space: nowrap;
    display: flex;
    align-items: center;
}

.more-info-method {
    font-size: 10px;
    margin-left: 3px;
}

.today-img {
    
    height: 200px;
    position: absolute;
    top: 20%;
    left: 83%;
    transform: translate(-50%, -50%);
}


.cloudy {
    top: 60%;
}

.partly-cloudy-night{
    top: 45%;
}

.rain {
    height: 291px;
    top: 47%;
}

.clear-day {
    top: 44%;
}

.clear-night{
    height: 150px;
    top: 50%;
}

.more-info {
    backdrop-filter: blur(50px);
}

.hi {
    color: #ff2c2c;
}

.low {
    color: #1818f1;
}

.bxs-droplet {
    color: lightblue;
}

.bxs-thermometer {
    color: crimson;
}

.bx-wind {
    color: #767676;
    font-size: 17px;
}

.bxs-map{
    margin-right: 2px;
}
</style>