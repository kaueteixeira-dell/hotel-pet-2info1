<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink } from 'vue-router';

const API_URL = 'http://localhost:3000';

const pets = ref([]);
const tutores = ref([]);
const loading = ref(true);
const erro = ref('');

// async retorna uma Promise; await pausa esta função, mas não bloqueia a página.
async function carregarPets() {
  loading.value = true;
  erro.value = '';

  try {
    // fetch inicia a requisição e devolve uma Promise com a resposta HTTP.
    const respostaPets = await fetch(`${API_URL}/pets`);

    // fetch não considera respostas HTTP 4xx/5xx como falha automaticamente.
    if (!respostaPets.ok) {
      throw new Error(`A API respondeu com o status ${respostaPets.status}.`);
    }

    // json() também é assíncrono: aguarda o corpo ser lido e convertido.
    const dadosPets = await respostaPets.json();
    if (!Array.isArray(dadosPets)) {
      throw new Error('A resposta da API não contém uma lista de pets válida.');
    }
    pets.value = dadosPets;

    const respostaTutores = await fetch(`${API_URL}/tutores`);
    if (!respostaTutores.ok) {
      throw new Error(`A API respondeu com o status ${respostaTutores.status}.`);
    }

    const dadosTutores = await respostaTutores.json();
    if (!Array.isArray(dadosTutores)) {
      throw new Error('A resposta da API não contém uma lista de tutores válida.');
    }
    tutores.value = dadosTutores;
  } catch (error) {
    // Falhas de rede, HTTP ou conversão dos dados são apresentadas na tela.
    erro.value = error instanceof Error ? error.message : 'Não foi possível carregar os pets. Tente novamente.';
  } finally {
    loading.value = false;
  }
}

function nomeDoTutor(tutorId) {
  for (const tutor of tutores.value) {
    if (tutor.id === tutorId) {
      return tutor.nome;
    }
  }
  return 'Tutor Não Encontrado!';
}

onMounted(carregarPets);
</script>

<template>
  <div>
    <header class="d-flex flex-wrap align-items-center justify-content-between gap-3 mb-4">
      <div>
        <h1 class="h2 mb-1 fw-bold">Pets cadastrados</h1>
        <p class="text-body-secondary mb-0">Pets e seus tutores.</p>
      </div>

      <RouterLink class="btn btn-success" :to="{ name: 'addPet' }">
        <i class="bi bi-plus-lg me-2" aria-hidden="true"></i>Novo pet
      </RouterLink>
    </header>

    <p v-if="loading" role="status">Carregando pets...</p>
    <p v-else-if="erro" role="alert">Erro ao carregar os pets: {{ erro }}</p>
    <p v-else-if="pets.length === 0">Nenhum pet cadastrado.</p>

    <div v-else class="table-responsive">
      <table class="table table-hover align-middle">
        <thead>
          <tr>
            <th scope="col">Nome</th>
            <th scope="col">Espécie</th>
            <th scope="col">Tutor</th>
            <th scope="col"><span class="visually-hidden">Ações</span></th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="pet in pets" :key="pet.id">
            <th scope="row">{{ pet.nome }}</th>
            <td>{{ pet.especie }}</td>
            <td>{{ nomeDoTutor(pet.tutorId) }}</td>
            <td class="text-end">
              <RouterLink
                class="btn btn-sm btn-link link-success fw-semibold text-decoration-none"
                :to="{ name: 'petDetail', params: { id: pet.id } }"
              >
                Ver detalhes
              </RouterLink>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
