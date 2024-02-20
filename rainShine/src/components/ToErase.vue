<template>
  <div class="drop-down" @click="chooseActive = !chooseActive">
    <i class="bx bxs-map"></i>
  </div>
  <ul class="drop-down-list" v-show="chooseActive">
    <li id="search-location-container"><search-locations :locationsArray="arrayToFilter" @searchResults="searchUpdates" />
    </li>
    <li v-for="(location, i) in locationsArray" :key="i">{{ location }}</li>
  </ul>
</template>



<script setup>
import SearchLocations from './SearchLocations.vue';
import { ref, defineComponent } from 'vue';

defineComponent({ SearchLocations });

let chooseActive = ref(false);
let locationsArray = ref([]);
let arrayToFilter = ref([]);

// get cities //
const MIN_POPULATION_THRESHOLD = 100000;
const majorCities = ({ data }) => data.filter((city) => Number(city.populationCounts[0].value) >= MIN_POPULATION_THRESHOLD);
const fetchLocations = async () => {
  const res = await fetch('https://countriesnow.space/api/v0.1/countries/population/cities');
  const flag = await fetch('https://countriesnow.space/api/v0.1/countries/flag/images');
  if (!res.ok || !flag.ok) {
    throw new Error('Failed to fetch data');
  }

  const data = await res.json();
  //const flagData = await flag.json();

  //     locationsArray.value = majorCities(data).map(city => {
  //   const matchingFlagCountry = flagData.data.find(flagCity => flagCity.name === city.country);
  //   const flagImage = matchingFlagCountry ? `<img src="${matchingFlagCountry.flag}" alt="${matchingFlagCountry.name} Flag">` : '';
  //   return `${city.city}, ${city.country} ${flagImage}`;
  // });
  //const joinedData = [...majorCities(data), ...isoData.data];
  //console.log("joining🫲", joinedData);
  locationsArray.value = majorCities(data).map(city => { return `${city.city?.split(' ').slice(0, 2).join(' ')}, ${city.country.split(' ').slice(0, 4).join(' ')}` }).sort();
  arrayToFilter.value = [...locationsArray.value];
};

const getByPc = () => {
  return new Promise((resolve, reject) => {
    // confirm = window.confirm('RainDrop wants to use your location?');
    let confirm = true;
    if (confirm && navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        function (position) {
          const latitude = position.coords.latitude;
          const longitude = position.coords.longitude;
          resolve(`${latitude},${longitude}`);
        },
        (error) => reject(error)
      );
    } else {
      reject(new Error("Geolocation is not supported by this browser... hope you don't get caught in bad weather☔"));
    }
  })
};
getByPc()
  .then((location) => {
    console.log('location', location);
  })
  .catch((error) => {
    console.error('Error getting location:', error);
  });


fetchLocations();

const searchUpdates = (searchResults) => {
  locationsArray.value = searchResults;
}


</script>

<style>
.drop-down {
  background: lightblue;
  width: 250px;
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
  width: 290px;
  position: fixed;

}

.drop-down:hover,
.drop-down-list:hover {
  cursor: pointer;
}

.drop-down-list {
  background: #dbe2e4;
  white-space: nowrap;
  width: 320px;
}

li {
  white-space: nowrap;
  overflow: hidden;
  font-size: 14px;
}

.overflowing {
  will-change: transform;
  animation: marquee 32s linear infinite;
}


@keyframes marquee {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(-50%);
  }
}

.drop-down-list li:hover {
  background: rgb(231, 249, 255);
}

.bx {
  font-size: 20px;
  margin-right: 5px;
}

ul {
  width: 200px;
  list-style: none;
  padding: 0;
  position: absolute;
  /* top: 40px; real */
  top: 201px;
  /*to erase*/
  left: 359px;
  /*to erase*/
  z-index: 2;
  height: 200px;
  overflow-y: scroll;
  scrollbar-width: none;
  /* For Firefox */
  -ms-overflow-style: none;
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


li {
  width: 100%;
  text-align: center;
  padding: 10px 40px;
}</style>