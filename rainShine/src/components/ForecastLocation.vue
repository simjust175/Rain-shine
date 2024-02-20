<template>
  <div class="drop-down" @click="state.chooseActive = !state.chooseActive">
    <i :class="arrow"></i>
    {{ state.locationChoice }}
  </div>

  <ul class="drop-down-list" v-show="state.chooseActive">
    <li id="search-location-container">
      <search-locations :locationsArray="state.arrayToFilter" @searchResults="searchUpdates" />
    </li>
    <li v-for="(location, i) in state.locationsArray" :key="i" @click="locationChoiceHandler(location)">
      {{ location }}
    </li>
  </ul>
  <LocationByIP @gotByIP="locationByIp" class="get-by-ip-button" />
</template>

<script setup>
import SearchLocations from './SearchLocations.vue';
import LocationByIP from './LocationByIP.vue';
import { ref, computed, watch } from 'vue';

const emit = defineEmits(['forecastUpdate']);

const state = ref({
  chooseActive: false,
  locationChoice: 'Choose a location',
  locationMethod: 'New-york USA',
  locationsArray: [],
  arrayToFilter: [],
});

const arrow = computed(() => `bx bx-chevron-${state.value.chooseActive ? 'up' : 'down'}`);

const fetchData = async (url) => {
  const res = await fetch(url);
  if (!res.ok) {
    throw new Error(`Failed to fetch data: ${res.statusText}`);
  }
  return await res.json();
};

const weatherService = {
  apiKey: 'JFP3F6HVT2HYAB2NSQQQUAL2L',
  baseApiUrl: 'https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline',

  async getForecast(forecastArea) {
    console.log("methology....", forecastArea);
    const apiUrl = `${this.baseApiUrl}/${forecastArea}?unitGroup=metric&key=${this.apiKey}&contentType=json`;
    return fetchData(apiUrl);
  }
};

const locationChoiceHandler = (choice) => {
  state.value.locationChoice = choice;
  state.value.chooseActive = false;
  state.value.locationMethod = choice;
};

const locationByIp = (gotByIP) => {
  state.value.locationMethod = gotByIP;
};

const fetchForecast = async (area) => {
  if (area) {
    try {
      const forecast = await weatherService.getForecast(area);
      emit('forecastUpdate', forecast);
    } catch (error) {
      console.error(`Failed to get forecast: ${error.message}`);
    }
  }
}

//Update the location every time a new area is Emitted.
watch(() => state.value.locationMethod, (newArea) => {
  console.log("ip wTacher has been inialized", newArea);
  fetchForecast(newArea)
});

fetchForecast(state.value.locationMethod);

// ~~~~~~~~~~~~~||||||||<<< get locations >>>||||||||~~~~~~~~~ //
const searchUpdates = (searchResults) => {
  state.value.locationsArray = searchResults;
};

const MIN_POPULATION_THRESHOLD = 2000000;
const fetchLocations = async () => {
  try {
    const citiesResponse = await fetchData('https://countriesnow.space/api/v0.1/countries/population/cities');
    const majorCities = citiesResponse.data.filter(
      (city) => Number(city.populationCounts[0].value) >= MIN_POPULATION_THRESHOLD
    );

    state.value.locationsArray = majorCities
      .map(city => `${city.city?.split(' ').slice(0, 2).join(' ')}, ${city.country.split(' ').slice(0, 4).join(' ')}`)
      .sort();
    state.value.arrayToFilter = [...state.value.locationsArray];
  } catch (error) {
    console.error(`Failed to fetch locations: ${error.message}`);
  }
};
fetchLocations();
</script>

<style scoped>
.drop-down {
  background: lightblue;
  width: 320px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
}

#search-location-container {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  padding-top: 2px;
  height: 40px;
}

#search-location-container:hover {
  background: #dbe2e4;
}

#search-location {
  padding: 10px 20px;
  width: 310px;
  position: fixed;
}

.drop-down:hover,
.drop-down-list:hover {
  cursor: pointer;
}

ul {
  width: 200px;
  list-style: none;
  padding: 0;
  position: absolute;
  top: 40px;
  z-index: 2;
  height: 200px;
  overflow-y: scroll;
  scrollbar-width: none;
  /* For Firefox */
  -ms-overflow-style: none;
  ;
}

ul::-webkit-scrollbar {
  width: 0;
  color: transparent;
  /* Adjust width as needed */
}

ul::-webkit-scrollbar-track {
  background-color: transparent;
  /* Hide track */
}

.drop-down-list {
  background: #dbe2e4;
  white-space: nowrap;
  width: 320px;
}

.drop-down-list li:hover {
  background: rgb(231, 249, 255);
  cursor: pointer;
}

.bx {
  font-size: 20px;
  margin-right: 5px;
}


li {
  width: 100%;
  text-align: center;
  padding: 10px 40px;
}

.overflowing {
  will-change: transform;
  animation: marquee 32s linear infinite;
}

.get-by-ip-button {
  position: absolute;
  top: 0;
  right: 435px;
}

.clear-day {
  top: 47%;
}


@keyframes marquee {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(-50%);
  }
}
</style>