<template>
  <v-app>
    <v-main>
      <VRecommendationForm />
      <VModal />
    </v-main>
  </v-app>
</template>

<script>
import VRecommendationForm from "./components/VRecommendationForm.vue";
import VModal from "./components/VModal.vue";

export default {
  name: "App",
  components: {
    VRecommendationForm,
    VModal,
  },
};
</script>

<style></style>
