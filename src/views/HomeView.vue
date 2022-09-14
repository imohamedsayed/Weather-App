<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="search"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-ibm-secondary focus:outline-none focus:shadow-lg"
      />
      <ul
        v-if="searchResults"
        class="absolute bg-ibm-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
        <p v-if="searchError">Sorry Something went wrong, Please try again.</p>
        <p v-if="!serverError && searchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="item in searchResults"
            :key="item.id"
            @click="previewCity(item)"
            class="py-2 cursor-pointer"
          >
            {{ item.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkelton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script>
import { ref } from "@vue/reactivity";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";
import CityCardSkelton from "@/components/CityCardSkelton.vue";
// @ is an alias to /src

export default {
  name: "HomeView",
  components: { CityList, CityCardSkelton },
  setup() {
    let search = ref("");
    const apiKey =
      "pk.eyJ1IjoibW9oYW1tZWQtc2F5ZWQiLCJhIjoiY2w4MHpneWo1MGIyZzNvcjFqMWp1b21hbCJ9.2nS08WwA35K4LeqCrqDR3w";
    const queryTimeout = ref(null);
    const searchResults = ref(null);
    const searchError = ref(null);
    const getSearchResults = () => {
      clearTimeout(queryTimeout.value);
      queryTimeout.value = setTimeout(async () => {
        if (search.value !== "") {
          try {
            const result = await axios.get(
              `https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${apiKey}&type=place`
            );
            searchResults.value = result.data.features;
            console.log(searchResults.value);
          } catch {
            searchError.value = true;
          }
          return;
        }
        searchResults.value = null;
      }, 300);
    };
    const router = useRouter();
    const previewCity = (result) => {
      const [city, state] = result.place_name.split(",");
      router.push({
        name: "CityView",
        params: { state: state.replaceAll(" ", ""), city: city },
        query: {
          lat: result.geometry.coordinates[1],
          lng: result.geometry.coordinates[0],
          preview: true,
        },
      });
    };

    return {
      search,
      getSearchResults,
      searchResults,
      searchError,
      previewCity,
    };
  },
};
</script>
