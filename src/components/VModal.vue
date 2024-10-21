<template>
  <div>
    <v-dialog
      v-model="dialog"
      max-width="600px"
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar>
          <v-spacer></v-spacer>
          <v-btn icon="mdi-close" @click="dialog = false"></v-btn>
        </v-toolbar>
        <v-card-title class="headline">Recomendação</v-card-title>
        <v-card-text>
          <p>{{ recommendation }}</p>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from "vue";
import emitter from "../plugins/emitter";

export default {
  name: "RecommendationDialog",
  setup() {
    const dialog = ref(false);
    const recommendation = ref("");

    const openModal = (rec) => {
      recommendation.value = rec;
      dialog.value = true;
    };

    onMounted(() => {
      emitter.on("open-modal", openModal);
    });

    onUnmounted(() => {
      emitter.off("open-modal", openModal);
    });

    return {
      dialog,
      recommendation,
    };
  },
};
</script>

<style scoped>
.v-dialog {
  text-align: center;
}
</style>
