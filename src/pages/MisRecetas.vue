<template>
  <q-page padding class="bg-secondary column">
    <div v-if="keys.length" class="row justify-center">
      <h4 class="text-bold text-purple q-ma-md">Mis recetas</h4>
      <q-btn
        v-if="keys.length > 1"
        flat
        round
        color="primary"
        icon="sort_by_alpha"
        size="15px"
        @click="ordenar"
        class="absolute-top-right q-mt-lg q-mr-lg"
      />
    </div>
    <q-separator v-if="keys.length" class="q-mt-xs"></q-separator>
    <q-list separator>
      <q-item v-for="receta in keys" :key="receta" class="q-pl-xs q-pr-xs">
        <q-item-section>
          <h6 class="text-bold text-purple q-ma-xs">
            · {{ receta }}
          </h6></q-item-section
        >
        <q-item-section avatar>
          <q-fab
            color="primary"
            icon="keyboard_arrow_left"
            push
            round
            dense
            direction="left"
            padding="sm"
          >
            <q-fab-action
              push
              color="primary"
              round
              icon="visibility"
              dense
              @click.stop="
                obtenerReceta(receta);
                mostrar = true;
              "
            />
            <q-fab-action
              v-if="!receta.includes('(prop.)')"
              push
              color="primary"
              round
              icon="edit"
              dense
              @click.stop="
                obtenerReceta(receta);
                editar = true;
              "
            />
            <q-fab-action
              push
              color="primary"
              round
              icon="delete"
              dense
              @click.stop="eliminarReceta(receta)"
            />
          </q-fab>
        </q-item-section>
      </q-item>
    </q-list>
    <q-dialog v-model="mostrar">
      <q-card class="bg-primary">
        <q-card-section class="q-pl-sm q-pr-sm q-pb-sm">
          <q-btn
            icon="close"
            flat
            round
            dense
            v-close-popup
            padding="none"
            class="float-right"
          />
          <div class="text-h5 text-secondary text-bold text-center">
            {{ $receta.nombreReceta }}
          </div>
        </q-card-section>
        <q-card-section class="q-pt-none">
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
              style="max-height: 300px"
              class="scroll text-black text-body1 text-center text-justify"
            >
              <pre wrap class="q-ma-none">{{ $receta.descripcion }} </pre>
            </q-card-section>
          </div>
        </div>
        <q-card-section class="text-center">
          <q-btn
            v-if="!$receta.nombreReceta.includes('proporcion')"
            label="Calcular proporcion"
            icon-right="calculate"
            text-color="purple"
            color="secondary"
            style="width: 100%"
            @click="propor = true"
          />
        </q-card-section>
      </q-card>
    </q-dialog>
    <q-dialog v-model="propor">
      <q-card class="bg-primary">
        <q-card-section class="row q-pb-none">
          <div class="text-h5 q-pl-md text-black text-bold">
            En proporcion a..
          </div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <q-card-section>
          <q-form
            @submit.prevent="
              calcular($receta.nombreReceta, ingrediente, cantidad)
            "
          >
            <q-select
              class="q-pa-none bg-secondary q-mb-lg q-mt-xs"
              outlined
              clearable
              label="Ingrediente"
              v-model="ingrediente"
              :options="opciones"
              :rules="[
                (val) => (val && val.length > 0) || 'Este campo está vacío',
              ]"
            ></q-select>
            <q-input
              class="q-pa-none bg-secondary q-mb-lg q-mt-xs"
              outlined
              type="text"
              label="Cantidad"
              v-model="cantidad"
              lazy-rules
              :rules="[
                (val) =>
                  (val && !isNaN(val)) || 'Por favor, ingresa un número válido',
                (val) => (val && val.length > 0) || 'Este campo está vacío',
              ]"
            ></q-input>
            <q-btn
              label="Calcular"
              icon-right="calculate"
              text-color="purple"
              color="secondary"
              style="width: 100%"
              type="submit"
              class="q-mt-xs"
            />
          </q-form>
        </q-card-section>
      </q-card>
    </q-dialog>
    <q-dialog v-model="editar">
      <q-card class="bg-secondary">
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
            :editable="true"
            :addIng="true"
          ></TablaDeIngredientes>
        </q-card-section>
        <div class="bg-secondary q-pa-xs" style="border-radius: 10px">
          <div class="text-black row justify-around text-bold text-h6">
            Descripción:
          </div>
          <div class="row justify-around">
            <q-card-section
              style="max-height: 300px"
              class="scroll text-black text-body1 text-center text-justify"
            >
              <q-input
                filled
                v-model="edit_descripcion"
                autogrow
                style="min-width: 300px"
              >
              </q-input>
            </q-card-section>
          </div>
        </div>
        <q-card-section class="text-center">
          <q-btn
            label="Guardar"
            icon-right="save"
            color="primary"
            style="width: 100%"
            @click="guardarEdit"
          />
        </q-card-section>
      </q-card>
    </q-dialog>
    <div v-if="!keys.length" class="absolute-center text-center no-recetas">
      <q-icon name="sentiment_dissatisfied" size="90px" color="primary" />
      <div class="text-h5 text-primary">No hay recetas para mostrar</div>
    </div>

    <q-page-sticky
      :position="keys.length > 10 ? 'bottom-center' : 'bottom'"
      :offset="[0, 30]"
    >
      <q-btn
        round
        color="primary"
        icon="add"
        to="/newrecipe"
        size="17px"
        class="q-ma-none"
      />
    </q-page-sticky>
    <q-page-sticky position="top-right" :offset="[15, 25]"> </q-page-sticky>
  </q-page>
</template>

<script setup>
import { useQuasar } from "quasar";
import TablaDeIngredientes from "src/components/TablaDeIngredientes.vue";
import { ref } from "vue";

const $q = useQuasar();
const mostrar = ref(false);
const $receta = ref();
const opciones = ref();
const propor = ref(false);
const cantidad = ref(null);
const ingrediente = ref(null);
const edit_descripcion = ref();
const editar = ref(false);

const keys = ref($q.localStorage.getAllKeys());

const ordenar = () => {
  estaOrdenadoAlfabeticamente(keys.value)
    ? keys.value.reverse()
    : keys.value.sort();
};

const estaOrdenadoAlfabeticamente = (array) => {
  for (let i = 0; i < array.length - 1; i++) {
    if (array[i] > array[i + 1]) {
      return false;
    }
  }
  return true;
};

const obtenerReceta = (key) => {
  $receta.value = JSON.parse($q.localStorage.getItem(key));
  opciones.value = [];
  for (let i = 0; i < $receta.value.ingredientes.length; i++) {
    opciones.value.push($receta.value.ingredientes[i].ingrediente);
  }
  edit_descripcion.value = $receta.value.descripcion;
};

const eliminarReceta = (key) => {
  $q.dialog({
    title: "Eliminar receta",
    message: "¿Desea eliminar la receta '" + key + "'?",
    cancel: true,
    persistent: true,
  }).onOk(() => {
    $q.localStorage.remove(key);
    let index = keys.value.indexOf(key);
    if (index !== -1) {
      keys.value.splice(index, 1);
    }
    $q.notify({
      message: "Receta eliminada correctamente",
      type: "positive",
    });
  });
};

const guardarEdit = () => {
  $q.dialog({
    title: "Guardar cambios",
    message: "¿Desea guardar la receta editada?",
    cancel: true,
    persistent: true,
  }).onOk(() => {
    let recetaEdit;
    recetaEdit = {
      nombreReceta: $receta.value.nombreReceta,
      ingredientes: $receta.value.ingredientes,
      descripcion: edit_descripcion.value,
    };
    $q.localStorage.set($receta.value.nombreReceta, JSON.stringify(recetaEdit));
    editar.value = false;

    $q.notify({
      message: "Receta editada correctamente",
      type: "positive",
    });
  });
};
</script>

<style lang="scss">
.no-recetas {
  opacity: 0.7;
}
</style>

<script>
export default {
  methods: {
    calcular(name, ingre, cant) {
      // Navegar a la página de la calculadora con parámetros
      this.$router.push({
        path: "/calcu",
        query: { receta: name, ingrediente: ingre, cantidad: cant },
      });
    },
  },
};
</script>
