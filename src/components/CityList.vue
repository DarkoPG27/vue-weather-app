<script setup>
import axios from "axios";
import CityCard from "./CityCard.vue";

const savedCities = ref([]);

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

    const request = [];
    savedCities.value.forEach((city) => {
      request.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&exclude={part}&appid=ee541c0de29e1bd2a56852b086297436&units=metric`
        )
      );
    });

    const weatherData = await PromiseRejectionEvent.all(request);

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    });
  }
};

await getCities();
</script>

<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" />
  </div>
</template>
