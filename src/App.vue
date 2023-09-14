<script setup>
  import { ref, computed, watch } from 'vue';
  import Header from './components/Header.vue';
  import Button from './components/Button.vue'
  import { calcularTotalPagar } from './helpers'

  // Defino el State
  const cantidad = ref(10000);
  const meses = ref(6);
  const total = ref(0)
  const MIN = 0;
  const MAX = 20000;
  const STEP = 100;


  const formaterDinero = (valor) => {
    const formatter = new Intl.NumberFormat('es-ES', {
      style: 'currency',
      currency: 'EUR'
    })
    return formatter.format(valor);
  };

  // Pongo los recursos que quiero escuchar y que hacer - si quiero que se llame la funcion al inicio
  watch([cantidad, meses], () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value);
  }, /*{immediate: true}*/)

  const pagoMensual = computed(() => {
    return total.value / meses.value
  })
  const handleChangeDecremento = () => {
    if(cantidad.value > MIN) {
      cantidad.value = cantidad.value - STEP;
    }
  }

  const handleChangeIncremento = () => {
    if(cantidad.value < MAX) {
      cantidad.value = cantidad.value + STEP;
    }
  }

</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10 ">

    <Header />

    <div class="flex justify-between mt-10">
      <Button 
        :operador="'-'"
        @fn="handleChangeDecremento"
      />
      <Button 
        :operador="'+'"
        @fn="handleChangeIncremento"
      />
    </div>

    <div class="my-5">
      <input 
        type="range"
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
        :min="MIN"
        :max="MAX"
        :step="STEP"
        v-model.number="cantidad"
      />

      <p class="text-center my-10 text-5xl font-extrabold text-indigo-600">{{formaterDinero(cantidad)}}</p>

      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Elige un <span class="text-indigo-600">plazo</span> a pagar
      </h2>
      
      <select 
        name="plazo" 
        id="plazo" 
        class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5" 
        :value="meses"
        v-model.number="meses"
      >
        <option value="6">6 Meses</option>
        <option value="12">12 meses</option>
        <option value="24">24 meses</option>
      </select>

      <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5">
        <h2 class="text-2xl font-extrabold text-gray-500 text-center">
          Resumen <span class="text-indigo-600">de pagos</span>
        </h2>
        <p class="text-xl text-gray-500 text-center font-bold">Meses: {{meses}}</p>
        <p class="text-xl text-gray-500 text-center font-bold">Total a pagar: {{formaterDinero(total)}}</p>
        <p class="text-xl text-gray-500 text-center font-bold">Mensuales: {{ formaterDinero(pagoMensual) }} / mes</p>
      </div>
      <p v-else class="text-center mt-5 text-gray-600 text-bold text-xl">Añade una cantidad y plazo a pagar</p>
    </div>
    
  </div>
</template>



