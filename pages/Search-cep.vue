<template>
  <div class="items-center justify-center bg-gray-900 h-screen w-full pb-8">
    <Navbar />
    <Breadcrumb :items="breadcrumbItems" />
    <div class="grid grid-cols-1 w-[70%] sm:w-[30%] pb-2 justify-self-center text-white">
      <h1 class="mb-4 text-xl text-center">
        <b>Buscar endereço</b>
      </h1>
      <p class="text-center">
        Preencha o CEP da localidade para realizar a busca na API <b>ViaCEP</b>
      </p>
    </div>
    <form class="grid grid-cols-1 gap-6 w-[70%] sm:w-[30%] pt-4 pb-16 justify-self-center text-white " @submit.prevent="handleSubmit">
      <div>
        <label for="cep" class="block text-sm font-medium text-left mb-1">CEP</label>
        <input
          v-model="form.cep"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          type="text"
          placeholder="Ex.: 01035100"
          @input="checkCep"
        />
        <span v-if="errors.cep">CEP é obrigatório e deve ter 8 caracteres</span>
      </div>

      <div>
        <label for="uf" class="block text-sm font-medium text-left mb-1">Estado</label>
        <input
          v-model="form.uf"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: SP"
        />
        <span v-if="errors.uf">Estado é obrigatório</span>
      </div>

      <div>
        <label for="localidade" class="block text-sm font-medium text-left mb-1">Cidade</label>
        <input
          v-model="form.localidade"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: São Paulo"
        />
        <span v-if="errors.localidade">Cidade é obrigatória</span>
      </div>

      <div>
        <label for="bairro" class="block text-sm font-medium text-left mb-1">Bairro</label>
        <input
          v-model="form.bairro"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: República"
        />
        <span v-if="errors.bairro">Bairro é obrigatório</span>
      </div>

      <div>
        <label for="logradouro" class="block text-sm font-medium text-left mb-1">Rua</label>
        <input
          v-model="form.logradouro"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: Av. São João"
        />
        <span v-if="errors.logradouro">Rua é obrigatória</span>
      </div>

      <div>
        <label for="number" class="block text-sm font-medium text-left mb-1">Número</label>
        <input
          v-model="form.number"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: 16"
        />
        <span v-if="errors.number">Número é obrigatório</span>
      </div>

      <div>
        <label for="complement" class="block text-sm font-medium text-left mb-1">Complemento</label>
        <input
          v-model="form.complement"
          type="text"
          class="w-full bg-transparent border-b border-white focus:outline-none focus:border-white text-white"
          placeholder="Ex.: Apto. 116"
        />
      </div>

      <div class="text-right">
        <button type="submit" class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-1 px-2 rounded inline-flex items-center">
          Enviar
        </button>
      </div>
    </form>
    <Footer />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import Breadcrumb from '@/components/breadcrumb/breadcrumb.vue'

const breadcrumbItems = [
  { label: 'Home', to: '/' },
  { label: 'Buscar CEP', to: '/buscar-cep', isCurrent: true }
]

interface CepValues {
  cep: string;
  uf: string;
  localidade: string;
  bairro: string;
  logradouro: string;
  number: string;
  complement: string;
}

const form = ref<CepValues>({
  cep: '',
  uf: '',
  localidade: '',
  bairro: '',
  logradouro: '',
  number: '',
  complement: ''
})

const errors = ref({
  cep: false,
  uf: false,
  localidade: false,
  bairro: false,
  logradouro: false,
  number: false
})

const fetchCepData = async (cep: string) => {
  try {
    const response = await axios.get(`https://viacep.com.br/ws/${cep}/json/`)
    const data = response.data
    form.value.uf = data.uf || ''
    form.value.localidade = data.localidade || ''
    form.value.bairro = data.bairro || ''
    form.value.logradouro = data.logradouro || ''
  } catch (error) {
    // eslint-disable-next-line no-console
    console.error('Erro ao buscar o CEP:', error)
  }
}

const checkCep = () => {
  form.value.cep = form.value.cep.replace(/\D/g, '').slice(0, 8)

  if (form.value.cep.length === 8) {
    fetchCepData(form.value.cep)
  }
}

const handleSubmit = () => {
  errors.value.cep = form.value.cep.length !== 8
  errors.value.uf = !form.value.uf
  errors.value.localidade = !form.value.localidade
  errors.value.bairro = !form.value.bairro
  errors.value.logradouro = !form.value.logradouro
  errors.value.number = !form.value.number

  const hasErrors = Object.values(errors.value).some(error => error)

  if (!hasErrors) {
    Swal.fire({
      title: 'Sucesso!',
      text: 'Endereço validado com sucesso!',
      icon: 'success',
      confirmButtonText: 'Ok'
    })
    // eslint-disable-next-line no-console
    console.log(form.value)
  }
}
</script>

<style>
html, body{
  background-color: #111827;
}
</style>
