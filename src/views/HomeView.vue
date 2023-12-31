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
      <ul
        class="absolute bg-weather-secondary w-full text-white shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <p v-if="searchError">Something went wrong. Please try again</p>
        <p class="py-2" v-if="!searchError && mapboxSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="result in mapboxSearchResults"
            :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)"
          >
            {{ result.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
          <CityList/>
          <template #fallback>
            <CityCardSkeleton />
          </template>

      </Suspense>
    </div>
  </main>
</template>

<script setup lang="ts">
import CityCardSkeleton from "@/components/CityCardSkeleton.vue";
import CityList from "@/components/CityList.vue";
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const searchQuery = ref<string>("");
const queryTimeout = ref<number>(0);
const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const mapboxSearchResults = ref<any>(null);
const searchError = ref<any>(null);

const getSearchResults = (): void => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
      } catch (error) {
          searchError.value = true;
          mapboxSearchResults.value = [];
      }
      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};

const previewCity = (result: any): void => {
  console.log(result);
  const [city, state] = result.place_name.split(",");
  router.push({ name: "cityView",
   params: { city: city, state: state.trim() },
    query: { lat: result.geometry.coordinates[1], lng: result.geometry.coordinates[0],preview:"true" }
     });

};
</script>
