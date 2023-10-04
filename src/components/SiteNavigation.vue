<template>
<header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
    class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
    <RouterLink :to="{ name: 'home' }">
    <div class="flex items-center gap-3">
        <i class="fa-solid fa-sun text-2xl"></i>
        <p class="text-2xl">El tiempo local</p>
    </div>
    </RouterLink>
    <div class="flex gap-3 flex-1 justify-end">
    <i
    class="fa-solid fa-circle-info
        text-xl hover:text-weather-secondary
        duration-150 cursor-pointer"
        @click="toggleModal"
    ></i>
    <i
    class="fa-solid fa-plus
    hover:text-weather-secondary
    duration-150 cursor-pointer"
    @click="addCity"
    v-if="route.query.preview"
    >
    </i>
    </div>
    <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
    <div class="text-black">
        <h1 class="text-2xl mb-1">Acerca de:</h1>
        <p class="mb-4">
        El tiempo local le permite seguir el tiempo actual y
        clima futuro de las ciudades de su elección.
        </p>
        <h2 class="text-2xl">Como funciona:</h2>
        <ol class="list-decimal list-inside mb-4">
        <li>
            Busque su ciudad ingresando el nombre en el
            barra de búsqueda.
        </li>
        <li>
            Seleccione una ciudad dentro de los resultados, esto tomará
            usted al tiempo actual para su selección.
        </li>
        <li>
            Realice un seguimiento de la ciudad haciendo clic en el icono "+" en el
            parte superior derecha. Esto guardará la ciudad para verla en un
            más tarde en la página de inicio.
        </li>
        </ol>

        <h2 class="text-2xl">Eliminar ciudad</h2>
        <p>
            Si ya no deseas rastrear una ciudad, simplemente selecciona
            la ciudad dentro de la página de inicio. En la parte inferior del
            página, habrá una opción para eliminar la ciudad.
        </p>
    </div>
    </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { uid } from "uid";
import { ref } from "vue";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const LOCAL_SAVED_CITIES = 'savedCities'
const addCity = ()=>{
    if(localStorage.getItem(LOCAL_SAVED_CITIES)){
        savedCities.value = JSON.parse(localStorage.getItem(LOCAL_SAVED_CITIES));
    }
    const locationObj = {
        id: uid(),
        state: route.params.state,
        city: route.params.city,
        coords: {
            lat: route.query.lat,
            lng: route.query.lng,
        }
    };
    savedCities.value.push(locationObj);
    localStorage.setItem(
        LOCAL_SAVED_CITIES,
        JSON.stringify(savedCities.value)
        );
    
    let query = Object.assign({},route.query);
    delete query.preview;
    query.id = locationObj.id;
    router.replace({query})
    
};

const modalActive = ref(null)
const toggleModal = ()=>{
    modalActive.value = !modalActive.value;
}

</script>
