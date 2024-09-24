<template>
  <div class="flex flex-col flex-1 items-center">
    <!--Banner-->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!--Weather Overview-->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.dt * 1000).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.dt * 1000).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.main.temp) }}&deg;
      </p>
      <p>
        Feels like
        {{ Math.round(weatherData.main.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.weather[0].description }}
      </p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        alt="Weather icon"
      />
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!--Remove City-->
    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();

const getWeatherData = async (): Promise<any> => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=46a4e2f995c09b113e91733f46c46e87&units=metric`
    );

    // Delay
    await new Promise((resolve) => setTimeout(resolve, 1000));

    return weatherData.data;
  } catch (error) {
    console.log(error);
  }
};

const weatherData = await getWeatherData();

const removeCity = (): void => {
  const cities = JSON.parse(localStorage.getItem("savedCities") as string);
  const updatedCities = cities.filter((city: any) => city.id !== route.query.id);

  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({ name: "home" });
};
</script>



<style scoped>
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

::-webkit-scrollbar-thumb {
  border-radius: 3px;
}

::-webkit-scrollbar-thumb:hover {
  background-color: #888;
}

::-webkit-scrollbar-track {
  background-color: transparent;
  border-radius: 3px;
}
</style>
