<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
import { uid } from 'uid';
import TheForm from './components/TheForm.vue';
import TheHeader from './components/TheHeader.vue';
import PacienteLista from './components/PacienteLista.vue';

const pacientes = ref([]);

const paciente = reactive({
  id: null,
  nombre: '',
  propietario: '',
  email: '',
  alta: '',
  sintomas: ''
});

onMounted(() => {
  pacientes.value = JSON.parse(localStorage.getItem('pacientes')) ?? [];
});

const guardarPaciente = () => {
  if ( paciente.id ) {
    const index = pacientes.value.findIndex(pacienteState => pacienteState.id === paciente.id);
    pacientes.value[index] = {...paciente};
  } else {
    pacientes.value.push({...paciente, id: uid()});
  }
  paciente.nombre = '';
  paciente.propietario = '';
  paciente.email = '';
  paciente.alta = '';
  paciente.sintomas = '';
  paciente.id = null;
}

const actualizarPaciente = (id) => {
  const pacienteEditar = pacientes.value.filter(paciente => paciente.id === id)[0];
  Object.assign(paciente, pacienteEditar);
}

const eliminarPaciente = (id) => {
  pacientes.value = pacientes.value.filter(paciente => paciente.id !== id);
}

const guardarStorage = () => {
  localStorage.setItem('pacientes', JSON.stringify(pacientes.value));
}

watch(pacientes, () => {
  guardarStorage();
}, {
  deep: true
});
</script>

<template>
  <div class="container mx-auto mt-12">
    <TheHeader />

    <div class="mt-12 md:flex">
      <TheForm
        v-model:nombre="paciente.nombre"
        v-model:propietario="paciente.propietario"
        v-model:email="paciente.email"
        v-model:alta="paciente.alta"
        v-model:sintomas="paciente.sintomas"
        @guardar-paciente="guardarPaciente"
        :id="paciente.id"
      />

      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">
          Administra tus
          <span class="text-indigo-600 font-bold">Pacientes</span>
        </h3>

        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Información de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <PacienteLista
            v-for="paciente in pacientes"
            :key="paciente.nombre"
            :paciente="paciente"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-10 text-2xl text-center">No hay pacientes</p>
      </div>
    </div>
  </div>
</template>
