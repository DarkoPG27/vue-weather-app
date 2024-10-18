<script setup>
import axios from "axios";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";
import { ref } from "vue";

const savedCities = ref([]);

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

    const request = [];
    savedCities.value.forEach((city) => {
      request.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coord.lat}&lon=${city.coord.lng}&exclude={part}&appid=ee541c0de29e1bd2a56852b086297436&units=metric`
        )
      );
    });

    const weatherData = await Promise.all(request);

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    });
  }
};

await getCities();

const router = useRouter();

const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: { lat: city.coord.lat, lng: city.coord.lng },
  });
};
</script>

<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No location added. To start tracking a location, search in the field above.
  </p>
</template>
