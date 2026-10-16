<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';

const pets = ref([]);
const tutores = ref([]);
const loading = ref(true);

async function carregarDados() {
  const respostaPets = await fetch(`${API_URL}/pets`);
  pets.value = await respostaPets.json();

  console.log('Pets:', pets.value);

  const respostaTutores = await fetch(`${API_URL}/tutores`);
  tutores.value = await respostaTutores.json();

  console.log('Tutores:', tutores.value);

  loading.value = false;
}


async function excluirTutor(id) {
  const confirmarExclusao = window.confirm('Deseja realmente excluir este item?')
  if (confirmarExclusao) {
    await fetch(`${API_URL}/tutores/${id}`, {
      method: 'DELETE',
    });
    await carregarDados();
    //Lógica para excluir o pet

  }
}

// Recebe o tutorId do pet e procura o tutor correspondente.
onMounted(carregarDados);
</script>

<template>
  <div>
    <header class="mb-4">
      <h1 class="text-2xl font-bold">Listagem de Tutores</h1>
      <p class="text-body-secondary mb-0">
        Listagem dos Tutores cadastrados no sistema.
      </p>
    </header>

    <RouterLink
      class="btn btn-primary"
      :to="{ name: 'addTutor' }"
    >
      Adicionar Tutor
    </RouterLink>

    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome</th>
          <th>Telefone</th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="tutor in tutores"
          :key="tutor.id"
        >
          <td>{{ tutor.id }}</td>
          <td>{{ tutor.nome }}</td>
          <td>{{ tutor.telefone }}</td>
          <td>
            <RouterLink :to="`/tutores/${tutor.id}`"> Visualizar </RouterLink>
            <button class="btn btn-danger mr-5" @click="excluirTutor(tutor.id)" :to="`/tutores/${tutor.id}`"> Excluir </Button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
