<template>
  <input type="search" name="search-location" id="search-location"
                placeholder="Search location" v-model="searchQuery" @input="handleSearch">
</template>

<script setup>
import { ref, defineProps, defineEmits } from 'vue';
const props = defineProps({ locationsArray: Array });
const emit = defineEmits(["searchResults"]);
let searchQuery = ref('');
const handleSearch = () => {
    let locations = [...props.locationsArray];
    emit("searchResults", locations.filter(city => city.toLowerCase().includes(searchQuery.value.toLowerCase())));
}


</script>

<style>

</style>