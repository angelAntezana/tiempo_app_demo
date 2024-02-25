<template>
    <div v-for="city in savedCities"
    :key="city.id"
    >
        <CityCard :city="city" @click="gotToCityView(city)">

        </CityCard>
    </div>

    <p v-if="savedCities.length===0">
        No location added. To start tracking a location, search in the field above.
    </p>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import CityCard from './CityCard.vue';

const LOCAL_SAVED_CITIES = 'savedCities';
const BASE_URL_WEATHER = import.meta.env.VITE_BASEURL_WEATHER;
const API_KEY = import.meta.env.VITE_APIKEY;

const savedCities = ref([]);
const getCities = async ()=>{
    if(localStorage.getItem(LOCAL_SAVED_CITIES)){
        savedCities.value = JSON.parse(
            localStorage.getItem(LOCAL_SAVED_CITIES)
            );
    const requests = [];
    savedCities.value.forEach((city)=>{
        requests.push(
            axios.get(`${BASE_URL_WEATHER}?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${API_KEY}&units=metric`)
        );
    });

    const weatherData = await Promise.all(requests);

    // Flicker Delay
    await new Promise((res)=> setTimeout(res,1000));
    
    weatherData.forEach((value,index)=>{
        savedCities.value[index].weather = value.data;
    })

    }
};
await getCities();

const router = useRouter();
const gotToCityView = (city)=>{
    router.push({
        name:"cityView",
        params:{state:city.state, city: city.city},
        query: {id:city.id, lat: city.coords.lat, lng: city.coords.lng},
    })
}
</script>

