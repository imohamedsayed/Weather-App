<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCity(city)" />
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search the field above.
  </p>
</template>

<script setup>
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";
const { ref } = require("@vue/reactivity");
const { default: axios } = require("axios");

const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const requests = [];
  savedCities.value.forEach((city) => {
    requests.push(
      axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
      )
    );
  });

  const weatherData = await Promise.all(requests);
  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.data;
  });
};

await getCities();
const router = useRouter();

const goToCity = (city) => {
  router.push({
    name: "CityView",
    params: {
      state: city.state,
      city: city.city,
    },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};
</script>

<style></style>
