<template>
    <div class="getMyLocation" @click="emitIP">
        <i class='bx bx-current-location'></i>
    </div>
</template>

<script setup>
import { defineEmits } from 'vue';
const emit = defineEmits(["gotByIP"]);

const getByIP = () => {
    return new Promise((resolve, reject) => {
        const confirm = window.confirm('RainDrop wants to use your location?');
        //const confirm = true;
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
            reject(
                new Error(
                    "Geolocation is not supported by this browser... hope you don't get caught in bad weather☔"
                )
            );
        }
    });
};

const emitIP = async () => {
    try {
        const userLocation = await getByIP();
        console.log("🗺️", userLocation);
        emit("gotByIP", userLocation);
    } catch (error) {
        console.error('Error getting location:', error);
    }
};
</script>

<style>
.getMyLocation {
    display: flex;
    justify-content: center;
    align-items: center;
    background: lightblue;
    color: rgb(40, 152, 189);
    border-radius: 0.5rem;
    padding: 0.5rem;
    overflow: hidden;
    font-size: 32px;
}

.getMyLocation:hover {
    cursor: pointer;
    color: antiquewhite;
}
</style>