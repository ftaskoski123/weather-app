<template>
    <main class="container text-white">
      <div class="pt-4 mb-8 relative">
        <input
          type="text"
          @input="getSearchResults()"
          v-model="searchQuery"
          placeholder="Search for a city or state"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
        />
      <ul  class="absolute bg-weather-secondary w-full text-white shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <li  v-for="result in mapboxSearchResults" :key="result.id" class="py-2 cursor-pointer">{{ result.place_name }}</li>
      </ul>
      </div>
    </main>
  </template>


<script setup lang="ts">
import axios from "axios";
import { ref } from "vue";

const searchQuery = ref<string>("");
const queryTimeout = ref<number>(0);
  const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const mapboxSearchResults = ref<any>(false);
const getSearchResults = () : void => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      const result = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
      );
      mapboxSearchResults.value = result.data.features;
      console.log(mapboxSearchResults.value);
      return;
    }
    mapboxSearchResults.value = false;
  }, 300);
};

</script> 