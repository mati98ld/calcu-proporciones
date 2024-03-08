<template>
  <q-page padding class="bg-secondary column">
    <div v-if="keys.length">
      <h4 class="row justify-around text-bold text-purple q-ma-md">
        Mis recetas:
      </h4>
      <q-separator class="q-mt-xs"></q-separator>
    </div>
    <q-list separator>
      <q-item v-for="receta in keys" :key="receta" class="q-pl-md">
        <q-item-section>
          <h5 class="text-bold text-purple q-ma-xs">
            - {{ receta }}
          </h5></q-item-section
        >
        <q-item-section avatar>
          <q-btn
            push
            color="primary"
            round
            icon="visibility"
            dense
            @click.stop="obtenerReceta(receta)"
          />
        </q-item-section>
      </q-item>
    </q-list>
    <q-dialog v-model="mostrar">
      <q-card class="bg-primary">
        <q-card-section class="row q-pb-none">
          <div class="text-h5 q-pl-md text-black text-bold">
            {{ $receta.nombreReceta }}
          </div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <q-card-section>
          <TablaDeIngredientes
            :ingredientes="$receta.ingredientes"
          ></TablaDeIngredientes>
        </q-card-section>
        <div
          class="bg-secondary q-pa-xs q-ml-md q-mr-md"
          style="border-radius: 10px"
        >
          <div class="text-black row justify-around text-bold text-h6">
            Descripción:
          </div>
          <div class="row justify-around">
            <q-card-section
              style="max-height: 45vh"
              class="scroll text-black text-body1 text-center"
            >
              {{ $receta.descripcion }}
            </q-card-section>
          </div>
        </div>
        <q-card-section class="text-center">
          <q-btn
            label="Calcular proporcion"
            icon-right="calculate"
            text-color="purple"
            color="secondary"
            style="width: 100%"
          />
        </q-card-section>
      </q-card>
    </q-dialog>
    <div v-if="!keys.length" class="absolute-center text-center no-recetas">
      <q-icon name="sentiment_dissatisfied" size="90px" color="primary" />
      <div class="text-h5 text-primary">No hay recetas para mostrar</div>
    </div>

    <q-page-sticky position="bottom-right" :offset="[20, 20]">
      <q-btn round color="primary" icon="add" to="/newrecipe" size="20px" />
    </q-page-sticky>
  </q-page>
</template>

<script setup>
import { useQuasar } from "quasar";
import TablaDeIngredientes from "src/components/TablaDeIngredientes.vue";
import { ref } from "vue";

const $q = useQuasar();
const mostrar = ref(false);
const $receta = ref();

const obtenerReceta = (key) => {
  $receta.value = $q.localStorage.getItem(key);
  mostrar.value = true;
};
const keys = ref($q.localStorage.getAllKeys());
</script>

<style lang="scss">
.no-recetas {
  opacity: 0.7;
}
</style>
