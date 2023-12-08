<template>
  <url />
  <RouterView />
</template>

<script>
import url from "./components/url.vue";
import { RouterView } from "vue-router";

export default {
  components: {
    url,
  },
};
</script>

<style scoped></style>
