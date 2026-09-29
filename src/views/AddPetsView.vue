<script setup>
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';

const API_URL = 'http://localhost:3000';
const router = useRouter();

const tutores = ref([]);
const nome = ref('');
const especie = ref('');
const tutorId = ref('');
const loadingTutores = ref(true);
const salvando = ref(false);
const erro = ref('');

async function carregarTutores() {
  try {
    const resposta = await fetch(`${API_URL}/tutores`);
    if (!resposta.ok) {
      throw new Error('Não foi possível carregar a lista de tutores.');
    }

    tutores.value = await resposta.json();
  } catch (error) {
    erro.value = error instanceof Error ? error.message : 'Ocorreu um erro ao carregar os tutores.';
  } finally {
    loadingTutores.value = false;
  }
}

async function salvarPet() {
  erro.value = '';
  salvando.value = true;

  try {
    const resposta = await fetch(`${API_URL}/pets`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        nome: nome.value.trim(),
        especie: especie.value.trim(),
        tutorId: tutorId.value,
      }),
    });

    if (!resposta.ok) {
      throw new Error('Não foi possível salvar o pet. Tente novamente.');
    }

    await router.push({ name: 'pets' });
  } catch (error) {
    erro.value = error instanceof Error ? error.message : 'Ocorreu um erro ao salvar o pet.';
  } finally {
    salvando.value = false;
  }
}

onMounted(carregarTutores);
</script>

<template>
  <section class="container-fluid px-0">
    <header class="mb-4">
      <h1 class="h2 fw-bold mb-0">Cadastrar pet</h1>
    </header>

    <p v-if="loadingTutores" role="status">Carregando tutores...</p>
    <div v-else-if="erro && tutores.length === 0" class="alert alert-danger" role="alert">
      {{ erro }}
    </div>

    <form v-else class="row g-4" @submit.prevent="salvarPet">
      <div class="col-lg-7">
        <label for="nome-pet" class="form-label fw-semibold">Nome do pet</label>
        <input
          id="nome-pet"
          v-model="nome"
          class="form-control"
          name="nome"
          autocomplete="off"
          required
        />
      </div>

      <div class="col-lg-4">
        <label for="tutor-pet" class="form-label fw-semibold">Tutor</label>
        <select
          id="tutor-pet"
          v-model="tutorId"
          class="form-select"
          name="tutorId"
          required
        >
          <option value="" disabled>Selecione um tutor</option>
          <option v-for="tutor in tutores" :key="tutor.id" :value="tutor.id">
            {{ tutor.nome }}
          </option>
        </select>
      </div>

      <div class="col-lg-7">
        <label for="especie-pet" class="form-label fw-semibold">Espécie</label>
        <input
          id="especie-pet"
          v-model="especie"
          class="form-control"
          name="especie"
          placeholder="Ex.: Cachorro"
          required
        />
      </div>

      <div v-if="erro" class="col-12">
        <div class="alert alert-danger mb-0" role="alert">{{ erro }}</div>
      </div>

      <div class="col-12 d-flex justify-content-end">
        <button class="btn btn-success" type="submit" :disabled="salvando || tutores.length === 0">
          <span v-if="salvando" class="spinner-border spinner-border-sm me-2" aria-hidden="true"></span>
          {{ salvando ? 'Salvando...' : 'Salvar pet' }}
        </button>
      </div>
    </form>
  </section>
</template>
