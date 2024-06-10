<script setup>
 import { ref, reactive, watch, onMounted } from 'vue'
 import { uid } from 'uid'
 import Header from './components/Header.vue';
 import Formulario from './components/Formulario.vue';
 import Paciente from './components/Paciente.vue';

    const pacientes = ref([])

    
    const paciente = reactive ({
        id: null,
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: ''
    
    })

    const actualizarPaciente = (id) =>{
      const pacienteEditar = pacientes.value.filter(paciente => paciente.id === id)[0]
      Object.assign(paciente, pacienteEditar)
    }

    const elimintarPaciente = (id)=>{
      pacientes.value = pacientes.value.filter(paciente => paciente.id !== id)
    }

    const guardarPaciente = () => {

      if(paciente.id){
        const {id} = paciente
        const i = pacientes.value.findIndex((pacienteState) => pacienteState.id === id)
        pacientes.value[i] = {...paciente}
      }
      else{
        pacientes.value.push({ 
        ...paciente, 
        id: uid() })
      }

      //Limpiar formulario al guardar
      Object.assign(paciente,{
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: '',
        id: null
      })
    }
    watch(pacientes,()=>{
      guardarLocalStorage()
    },{
      deep: true
    })
    onMounted(() => {
      const pacientesLS = localStorage.getItem('pacientes')
      if(pacientesLS){
        pacientes.value = JSON.parse(pacientesLS)
      }
    })

    const guardarLocalStorage = () => {
      localStorage.setItem('pacientes', JSON.stringify(pacientes.value))
    }

</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />
    <div class="mt-12 md:flex">
      <Formulario 
      v-model:nombre="paciente.nombre"
      v-model:propietario="paciente.propietario"
      v-model:email="paciente.email"
      v-model:alta="paciente.alta"
      v-model:sintomas="paciente.sintomas"
      @guardar-paciente="guardarPaciente"
      :id="paciente.id"
      />
      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">Administra tus Pacientes</h3>
        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Informacion de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <Paciente
            v-for="paciente in pacientes" 
            :paciente="paciente"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="elimintarPaciente"
          />
        </div>
        <p v-else class="text-center mt-20 text 2xl">No hay pacientes</p>
      </div>
    </div>
  </div>
</template>

