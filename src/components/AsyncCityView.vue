<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lng}&appid=9983727643e134037394ff475299d61a&units=metric`
    );

    await new Promise((res) => setTimeout(res, 1000));

    return weatherData;
  } catch (error) {
    console.log(error);
  }
};

const weatherData = await getWeatherData();

const router = useRouter();

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities?.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->

    <div
      v-if="route.query.preview === 'true'"
      class="text-white p-4 bg-weather-primary z w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.data.list[0].dt_txt).toLocaleDateString(
            "en-us",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )
        }}
        {{
          new Date(weatherData.data.list[0].dt_txt).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short",
            }
          )
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.data.list[0].main.temp) }}&deg;
      </p>
      <div class="text=center">
        <p>
          Feels Like
          {{ Math.round(weatherData.data.list[0].main.feels_like) }}&deg;
        </p>
      </div>
      <p class="capitalize">
        {{ weatherData.data.list[0].weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.data.list[0].weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!--Hourly Weather  -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4 capitalize">
          5 day weather forecast data with 3-hour step
        </h2>
        <div
          class="flex gap-10 overflow-x-scroll [&::-webkit-scrollbar]:w-2 [&::-webkit-scrollbar-track]:rounded-full [&::-webkit-scrollbar-track]:bg-gray-100 [&::-webkit-scrollbar-thumb]:rounded-full [&::-webkit-scrollbar-thumb]:bg-gray-300 dark:[&::-webkit-scrollbar-track]:bg-neutral-700 dark:[&::-webkit-scrollbar-thumb]:bg-neutral-500"
        >
          <div
            v-for="hourData in weatherData.data.list"
            :key="hourData.dt"
            class="flex flex-col gap-4 item-center"
          >
            <p class="whitespace-nowrap text-md text-center">
              {{
                new Date(hourData.dt_txt).toLocaleDateString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[100px] object-contain"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            />
            <p class="text-xl text-center">
              {{ Math.round(hourData.main.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <!-- <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex item-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H:{{ Math.round(day.temp.max) }}</p>
            <p>L:{{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div> -->

    <div
      class="flex items=center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>
