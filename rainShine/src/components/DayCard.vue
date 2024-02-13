<script setup>

import { ref, computed, inject, defineProps } from 'vue';
const props = defineProps({
  forecastInfo: Object
});

const weatherMethod = inject('methodUpdate');
// watch(()=> props.weatherMethod,(n)=> console.log("wathching fffccc", n), { deep : true});
const days = ['Error', 'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];


//day
const todayDate = ref(new Date(props.forecastInfo.datetime));
//problem that the date returns one day earlier (example instead of 0 it returns 1)?? that s why we added '+1';
let today = computed(() => days[todayDate.value.getDay() + 1]);

const timeOfDay = computed(() => {
  const time = new Date().getHours();
  return `background-img: url(/backgrounds/${time > 6 && time < 19 ? 'day' : 'night2'}.svg)`;
})



</script>

<template>
  <div class="main-card-container">
    <div class="day-name">
      <div id="day" :style="timeOfDay">{{ today }}</div>
      <div class="date">{{ new Date(forecastInfo.datetime).toLocaleDateString() }}</div>
    </div>

    <div class="temp-container">
      <h1>{{ weatherMethod(forecastInfo.temp) }}<span class="weatherMethod"></span></h1>
      <div class="hi-lo">
        <div class="hi-lo"><i class='bx bxs-up-arrow'></i> {{ forecastInfo.tempmax }} </div>
        <div class="hi-lo"><i class='bx bxs-down-arrow'></i> {{ forecastInfo.tempmin }} </div>
      </div>
    </div>

    <p>{{ forecastInfo.conditions }}</p>
    <img :src="`../../public/weather_icons/${forecastInfo.icon}.svg`" :alt="forecastInfo.icon" class="day-img">
    <div class="more-info">
      <p>Feels like: {{ forecastInfo.feelslike }}</p>
    </div>
  </div>
</template>

<style scoped>
.main-card-container {
  margin: 8px;
  box-shadow: 1px 1px 1px #535353;
  background: linear-gradient(lightblue, skyblue, #bcefff);
  /* background: url(../../public/backgrounds/36.svg); */
  background-position: center;
  border-radius: 30px;
  height: 240px;
  width: 180px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 0;
  padding-top: 0;
  padding-bottom: 0;
  overflow: hidden;
}

.temp-container {
  display: flex;
  flex-direction: column;
  align-content: center;
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -4px;
}

h3 {
  font-size: 1.2rem;
}

.bx {
  font-size: 20px;
  margin-right: 0;
}

.bxs-up-arrow {
  color: rgb(255, 80, 80);
}

.bxs-down-arrow {
  color: rgb(37, 76, 205);
  margin-left: 18px;
}

.day-name {
  width: 100%;
  text-align: center;
  background: rgb(158, 231, 255);
  font-size: 10px;
}

.day-name #day {
  font-size: 20px;

}

.day-img {
  height: 80px;
}

/* #day {
  background: red;
  background: url(/backgrounds/day.svg);
} */

.hi-lo {
  display: flex;
  align-items: center;
}

.weatherMethod {
  font-size: 20px;
}

.more-info {
  background: rgb(135, 79, 255);
  display: flex;
}

.more-info p {
  font-size: 10px;
}

@media (min-width: 1024px) {

  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>