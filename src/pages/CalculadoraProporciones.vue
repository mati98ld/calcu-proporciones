<template>
  <q-page padding class="bg-secondary">
    <div class="text-center q-pa-md q-mt-xs">
      <div class="text-h5 text-purple text-bold">
        {{ recetaOriginal.nombreReceta }}: {{ cantidad }}
        {{ ingredienteOriginal.unidad }} de {{ ingrediente }}
      </div>
    </div>
    <div>
      <TablaDeIngredientes
        :ingredientes="proporcion.ingredientes"
      ></TablaDeIngredientes>
    </div>
    <div
      class="text-purple row justify-around text-bold text-h6 q-pa-xs q-mt-md"
    >
      Descripción:
    </div>
    <div class="row justify-around">
      <div
        style="max-height: 300px; border: solid; border-color: purple"
        class="scroll text-black text-body1 q-pa-md text-justify"
      >
        <pre wrap class="q-ma-none"
          >{{ recetaOriginal.descripcion }}
        </pre>
      </div>
    </div>
    <div class="row justify-center">
      <q-btn
        label="Guardar proporcion"
        color="primary"
        class="q-ma-md col-6"
        @click="guardarProporcion"
      ></q-btn>
    </div>
  </q-page>
</template>

<script setup>
import { useRoute } from "vue-router";
import { useQuasar } from "quasar";
import TablaDeIngredientes from "src/components/TablaDeIngredientes.vue";
import { ref } from "vue";

const route = useRoute();
const $q = useQuasar();

const key = route.query.receta;
const ingrediente = route.query.ingrediente;
const cantidad = route.query.cantidad;

const recetaOriginal = JSON.parse($q.localStorage.getItem(key));
const proporcion = ref(JSON.parse(JSON.stringify(recetaOriginal)));

const ingredienteOriginal = recetaOriginal.ingredientes.find(
  (ingredient) => ingredient.ingrediente === ingrediente
);

for (let i = 0; i < proporcion.value.ingredientes.length; i++) {
  proporcion.value.ingredientes[i].cantidad = parseFloat(
    (
      (cantidad * proporcion.value.ingredientes[i].cantidad) /
      ingredienteOriginal.cantidad
    ).toFixed(2)
  );
}

proporcion.value.nombreReceta = recetaOriginal.nombreReceta += " (prop.)";

const guardarProporcion = () => {
  try {
    $q.localStorage.set(
      proporcion.value.nombreReceta,
      JSON.stringify(proporcion.value)
    );
    $q.notify({
      type: "positive",
      message: "Proporcion guardada exitosamente",
    });
  } catch (error) {
    $q.notify({
      type: "negative",
      message: "Error al guardar la receta",
    });
  }
};
</script>
