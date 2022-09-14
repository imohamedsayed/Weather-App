<template>
  <div>
    <Suspense>
      <AsyncCityViewVue />
      <template #fallback>
        <CityViewSkelton />
      </template>
    </Suspense>
  </div>
</template>

<script>
import AsyncCityViewVue from "@/components/AsyncCityView.vue";
import CityCardSkelton from "@/components/CityCardSkelton.vue";
import CityViewSkelton from "@/components/CityViewSkelton.vue";
export default {
  components: { AsyncCityViewVue, CityCardSkelton, CityViewSkelton },
};
</script>

<style></style>
