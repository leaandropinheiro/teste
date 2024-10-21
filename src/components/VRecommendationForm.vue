<template>
  <v-container class="fill-height">
    <v-row>
      <v-col>
        <v-card elevation="5">
          <!-- Cabeçalho do Card -->
          <v-container>
            <v-card-title>
              <h1>Recomendação de Computador</h1>
            </v-card-title>
            <v-card-subtitle>
              Escolha o computador adequado de acordo com suas necessidades e
              orçamento.
            </v-card-subtitle>
          </v-container>

          <!-- v-stepper com os passos de seleção -->
          <v-stepper
            alt-labels
            editable
            prev-text="Anterior"
            next-text="Próximo"
            :items="stepItems"
            elevation="0"
          >
            <!-- Step 1: Tipo de Uso -->
            <template v-slot:item.1 class="card-item">
              <v-card
                title="Qual o propósito principal do computador?"
                subtitle="Escolha se o computador será utilizado para entretenimento, como jogos ou vídeos, ou para atividades de trabalho."
                elevation="0"
              ></v-card>
              <v-container>
                <v-select
                  v-model="answers.usage"
                  :items="usages"
                  label="Escolha o uso principal do computador"
                  variant="outlined"
                  clearable
                  chips
                  hide-details
                ></v-select>
              </v-container>
            </template>

            <!-- Step 2: Tipo de Dispositivo -->
            <template v-slot:item.2 class="card-item">
              <v-card
                title="Que tipo de dispositivo você prefere?"
                subtitle="Escolha entre um computador portátil ou desktop."
                elevation="0"
              ></v-card>
              <v-container>
                <v-select
                  v-model="answers.deviceType"
                  :items="deviceTypes"
                  label="Escolha o tipo do dispositivo"
                  variant="outlined"
                  clearable
                  chips
                  hide-details
                ></v-select>
              </v-container>
            </template>

            <!-- Step 3: Orçamento -->
            <template v-slot:item.3 class="card-item">
              <v-card
                title="Qual é o orçamento disponível?"
                subtitle="Insira o valor aproximado que você planeja investir no computador."
                elevation="0"
              ></v-card>
              <v-container>
                <v-text-field
                  v-model="answers.budget"
                  label="Digite o orçamento aproximado (R$)"
                  variant="outlined"
                  clearable
                  chips
                  hide-details
                  type="number"
                ></v-text-field>
              </v-container>
            </template>

            <!-- Step 4: Tipo de Trabalho ou Nível de Performance -->
            <template
              v-if="answers.usage === 'Trabalho'"
              v-slot:item.4
              class="card-item"
            >
              <v-card
                title="Qual o tipo de trabalho?"
                subtitle="Escolha o tipo de trabalho que você irá realizar."
                elevation="0"
              ></v-card>
              <v-container>
                <v-select
                  v-model="answers.workType"
                  :items="workTypes"
                  label="Escolha o tipo de trabalho"
                  variant="outlined"
                  clearable
                  chips
                  hide-details
                ></v-select>
              </v-container>
            </template>
            <template
              v-if="answers.usage === 'Entretenimento'"
              v-slot:item.4
              class="card-item"
            >
              <v-card
                title="Nível de Performance"
                subtitle="Escolha o nível de performance desejado para o computador."
                elevation="0"
              ></v-card>
              <v-container>
                <v-select
                  v-model="answers.performance"
                  :items="performances"
                  label="Escolha o nível de performance"
                  variant="outlined"
                  clearable
                  chips
                  hide-details
                ></v-select>
              </v-container>
            </template>
          </v-stepper>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import { defineComponent, reactive, ref, computed, watch } from "vue";
import emitter from "../plugins/emitter";

export default defineComponent({
  name: "VRecommendationForm",
  setup() {
    const answers = reactive({
      usage: "",
      deviceType: "",
      workType: "",
      performance: "",
      budget: null,
    });

    const usages = ["Trabalho", "Entretenimento"];
    const deviceTypes = ["Notebook", "Desktop"];
    const workTypes = ["Engenharia", "Desenvolvimento", "Escritório"];
    const performances = ["Baixa", "Média", "Alta"];

    const stepItems = computed(() => {
      const items = ["Uso Principal", "Dispositivo", "Orçamento"];
      if (answers.usage === "Trabalho") {
        items.push("Tipo de Trabalho");
      } else if (answers.usage === "Entretenimento") {
        items.push("Performance");
      }
      return items;
    });

    const recommendation = computed(() => {
      let rec = "";

      if (!answers.budget || answers.budget <= 0) {
        rec = "Por favor, insira um orçamento válido.";
        return rec;
      }

      // Verificações para o uso "Trabalho"
      if (answers.usage === "Trabalho") {
        if (answers.workType === "Escritório" && answers.budget >= 2500) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook Lenovo com Intel Core i5, 8GB RAM e SSD de 240GB (aprox. R$ 2500)."
              : "Recomendado: PC Intel Core i5 com 8GB RAM e SSD de 240GB (aprox. R$ 2500).";
        } else if (
          answers.workType === "Engenharia" &&
          answers.budget >= 6000
        ) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook HP ZBook com Intel Core i7, 16GB RAM e SSD de 480GB (aprox. R$ 6000)."
              : "Recomendado: Workstation com Intel Core i7, 16GB RAM, SSD de 480GB (aprox. R$ 6000).";
        } else if (
          answers.workType === "Desenvolvimento" &&
          answers.budget >= 4000
        ) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook Dell XPS com Intel Core i7, 16GB RAM (aprox. R$ 4000)."
              : "Recomendado: PC Intel Core i7 com 16GB RAM (aprox. R$ 4000).";
        } else {
          // Orçamento insuficiente para o trabalho escolhido
          rec = "Orçamento insuficiente para a escolha feita.";
        }
      }

      // Verificações para o uso "Entretenimento"
      else if (answers.usage === "Entretenimento") {
        if (answers.performance === "Baixa" && answers.budget >= 2000) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook Acer Aspire com Intel Core i3, 8GB RAM e SSD de 120GB (aprox. R$ 2000)."
              : "Recomendado: PC com Intel Core i3, 8GB RAM e SSD de 120GB (aprox. R$ 2000).";
        } else if (answers.performance === "Média" && answers.budget >= 4000) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook Gamer Dell G5 com Intel Core i5, 16GB RAM, GTX 1650 (aprox. R$ 4000)."
              : "Recomendado: PC Gamer com Intel Core i5, 16GB RAM, GTX 1650 (aprox. R$ 4000).";
        } else if (answers.performance === "Alta" && answers.budget >= 6000) {
          rec =
            answers.deviceType === "Notebook"
              ? "Recomendado: Notebook Gamer ASUS ROG com Intel Core i7, 32GB RAM, RTX 3060 (aprox. R$ 6000)."
              : "Recomendado: PC Gamer com Intel Core i7, 32GB RAM, RTX 3060 (aprox. R$ 6000).";
        } else {
          rec = "Orçamento insuficiente para a escolha feita.";
        }
      }

      // Se nenhum uso foi selecionado
      else {
        rec = "Por favor, selecione um uso válido.";
      }

      console.log("Recomendação:", rec);
      return rec;
    });

    // Emitir o evento quando workType ou performance for selecionado
    watch(
      () => [answers.workType, answers.performance],
      ([newWorkType, newPerformance]) => {
        if (newWorkType || newPerformance) {
          console.log("Emitindo evento 'open-modal'", recommendation.value);
          emitter.emit("open-modal", recommendation.value);
        }
      }
    );

    return {
      answers,
      usages,
      deviceTypes,
      workTypes,
      performances,
      recommendation,
      stepItems,
      emitter,
    };
  },
});
</script>

<style>
.v-stepper-header {
  box-shadow: none !important;
}
.v-window-item {
  height: 250px !important;
}
</style>
