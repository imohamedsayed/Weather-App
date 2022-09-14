<template>
  <div class="flex flex-col min-h-screen font-Roboto hole-app">
    <SiteNavigation />
    <router-view />
  </div>
</template>

<script>
import SiteNavigation from "./components/SiteNavigation.vue";
export default {
  components: { SiteNavigation },
};
</script>
<style>
#app .hole-app {
  background: linear-gradient(#596174, #2e3440);
}
</style>
