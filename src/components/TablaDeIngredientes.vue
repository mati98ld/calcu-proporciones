<template>
  <q-table
    no-data-label="No hay ingredientes añadidos"
    :columns="columns"
    :rows="ingredientes"
    class="bg-secondary"
    :hide-bottom="!(ingredientes[0] == null)"
    :selection="seleccion"
    v-model:selected="selected"
    row-key="ingrediente"
  >
  </q-table>
  <div v-show="!(ingredientes[0] == null) && editable" class="row justify-end">
    <q-btn
      v-show="seleccion == 'single'"
      color="purple"
      outline
      @click="cancelar"
      class="q-pa-sm q-mr-sm"
      >cancelar</q-btn
    >
    <q-btn
      v-if="selected[0] == null"
      label="Eliminar un ingrediente"
      color="primary"
      @click="seccionEliminar"
      :disable="seleccion == 'single'"
    ></q-btn>
    <q-btn
      v-else
      label="Eliminar ingrediente seleccionado"
      color="primary"
      @click="eliminarIngrediente(ingredientes)"
    ></q-btn>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useQuasar } from "quasar";

const $q = useQuasar();

const columns = [
  {
    name: "ingrediente",
    label: "Ingrediente",
    align: "left",
    field: "ingrediente",
    sortable: true,
  },
  {
    name: "cantidad",
    label: "Cantidad",
    align: "center",
    field: "cantidad",
  },
  {
    name: "unidad",
    label: "Unidad",
    align: "right",
    field: "unidad",
    sortable: true,
  },
];

const selected = ref([]);

const cancelar = () => {
  seleccion.value = "none";
  selected.value = [];
};

const index = (arr, ele) => {
  let busqueda = JSON.stringify(ele[0]);
  return arr.findIndex(
    (ingredienteSelected) => JSON.stringify(ingredienteSelected) === busqueda
  );
};

const eliminarIngrediente = (array) => {
  $q.dialog({
    title: "Confirmar",
    message: "¿Desea eliminar el ingrediente seleccionado?",
    cancel: true,
    persistent: true,
  }).onOk(() => {
    array.splice(index(array, selected.value), 1);
    selected.value = [];
    seleccion.value = "none";
    $q.notify({
      message: "Ingrediente eliminado correctamente",
      type: "positive",
    });
  });
};

const seleccion = ref("none");

const seccionEliminar = () => {
  seleccion.value = "single";
};
</script>

<script>
export default {
  props: {
    ingredientes: Array,
    editable: Boolean,
  },
};
</script>
