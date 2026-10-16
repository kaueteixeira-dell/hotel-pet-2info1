<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRoute, useRouter } from 'vue-router';

const route = useRoute()
const router = useRouter()
const API_URL = 'http://localhost:3000'
//exibir informações do pet e do tutor
const pet = ref({})
const tutor = ref({})
const erroExclusao = ref('')
const excluindo = ref(false)


async function carregarPet(){
  //exibindo os dados do Id do Pet
  const idPet = route.params.id;
  console.log('Id do pet:', idPet);

  const respostePet = await fetch(`${API_URL}/pets/${idPet}`);

  pet.value = await resposta.json();

  const resportaTutor = await fetch(`${API_URL}/tutores/${pet.value.tutor.id}`);
  tutor.value = await respostaTutor.json();
}

async function excluirPet(){
  const idPet = route.params.id;
  erroExclusao.value = ''
  excluindo.value = true

  try {
    const resposta = await fetch(`${API_URL}/pets/${idPet}`, {
      method: 'DELETE',
    });
    if (!resposta.ok) {
      throw new Error('Não foi possível excluir o pet.')
    }
    await router.push({ name: 'pets' })
  } catch {
    erroExclusao.value = 'Não foi possível excluir o pet. Tente novamente.'
  }
}
</script>

<template>
  <h1> Nome do pet: {{ pet.nome }}</h1>
  <p> Espécie: {{ pet.especie }}</p>
  <p> Nome do Tutor: {{ tutor.nome }}</p>
  <p> Telefone: {{ tutor?.telefone }}</p>

  <button class="btn btn-default">
    <RouterLink
    :to="{name: 'pets'}">
    Volta
  </RouterLink>
  </button>

  <button class="btn btn-primary" :disabled="excluindo" @click="excluirPet">
    {{ excluindo ? 'Excluindo...' : 'Excluir' }}
  </button>



</template>
