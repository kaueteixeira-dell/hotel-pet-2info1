<script setup>
import { onMounted, ref } from 'vue';
import { RouterLink, useRoute } from 'vue-router';

const API_URL = 'http://localhost:3000';
const route = useRoute();

const pet = ref(null);
const tutor = ref(null);
const loading = ref(true);
const erro = ref('');

async function carregarPet() {
  try {
    const respostaPet = await fetch(`${API_URL}/pets/${route.params.id}`);
    if (!respostaPet.ok) {
      throw new Error('Não foi possível encontrar este pet.');
    }

    pet.value = await respostaPet.json();

    const respostaTutores = await fetch(`${API_URL}/tutores`);
    if (!respostaTutores.ok) {
      throw new Error('Não foi possível carregar os tutores.');
    }

    const tutores = await respostaTutores.json();
    tutor.value = tutores.find((item) => item.id === pet.value.tutorId) ?? null;
  } catch (error) {
    erro.value = error instanceof Error ? error.message : 'Ocorreu um erro ao carregar o pet.';
  } finally {
    loading.value = false;
  }
}

onMounted(carregarPet);
</script>

<template>
  <section>
    <p v-if="loading" role="status">Carregando detalhes do pet...</p>
    <div v-else-if="erro" class="alert alert-danger" role="alert">
      {{ erro }}
    </div>

    <template v-else-if="pet">
      <RouterLink
        class="link-success fw-semibold text-decoration-none"
        :to="{ name: 'pets' }"
      >
        <i class="bi bi-arrow-left me-2" aria-hidden="true"></i>Voltar para pets
      </RouterLink>

      <header class="mt-4 mb-4">
        <h1 class="display-6 fw-bold mb-2">{{ pet.nome }}</h1>
        <p class="text-body-secondary mb-0">Identificador: {{ pet.id }}</p>
      </header>

      <hr class="mb-4" />

      <dl class="row g-4">
        <div class="col-sm-6">
          <dt class="text-body-secondary small mb-2">Espécie</dt>
          <dd class="fs-5">{{ pet.especie }}</dd>
        </div>
        <div class="col-sm-6">
          <dt class="text-body-secondary small mb-2">Tutor</dt>
          <dd class="fs-5">{{ tutor?.nome ?? 'Tutor não encontrado' }}</dd>
        </div>
      </dl>
    </template>
  </section>
</template>
