<template>
  <div class="drop-down" @click="chooseActive = !chooseActive">
    <i :class='arrow'></i>
    {{ locationChoice }}
  </div>

  <ul class="drop-down-list" v-show="chooseActive">
    <li v-for="(location, i) in locationsArray" :key="i" @click="locationChoiceHandler(location)">{{ location }}</li>
  </ul>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';

let forecastArray = ref({}); //reactive([])
const emit = defineEmits(['forecastUpdate']);
let counter = 0;
const getForecast = async (lat, lon) => {
  try {
    const res = await fetch(`https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${lat},${lon}?unitGroup=metric&key=JFP3F6HVT2HYAB2NSQQQUAL2L&contentType=json`);
    const { days, timezone, currentConditions: { temp, feelslike, humidity, conditions, icon } } = await res.json(); console.log("res", res);
    console.log("res", days[0], counter++);
    forecastArray.value = { days, timezone, temp, feelslike, humidity, conditions, icon };
    emit('forecastUpdate', forecastArray.value)
  } catch (error) {
    console.log(`Error in loading content: ${error.message}`);
  }
};


if (navigator.geolocation) {
  navigator.geolocation.getCurrentPosition(function (position) {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    return getForecast(latitude, longitude);
  });
} else {
  console.log("Geolocation is not supported by this browser... hope you don't get caught in bad weather...");
}

let locationsArray = ref(['Jerusalem', 'New-york', 'Brussels']);
let state = reactive('down');
let chooseActive = ref(false);
let locationChoice = ref('Choose a location')
let arrow = computed(() => `bx bx-chevron-${state}`);

const locationChoiceHandler = (choice) => {
  locationChoice.value = choice;
  chooseActive.value = false;
}

</script>

<style scoped>
.drop-down {
  background: lightblue;
  width: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
}

.drop-down:hover,
.drop-down-list:hover {
  cursor: pointer;
}

.drop-down-list {
  background: #dbe2e4;
}

.drop-down-list li:hover {
  background: rgb(231, 249, 255);
}

ul {
  width: 200px;
  list-style: none;
  padding: 0;
  position: absolute;
  z-index: 2;
}

li {
  width: 100%;
  text-align: center;
}
</style>