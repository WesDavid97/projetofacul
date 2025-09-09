<template>
  <div class="container-fluid mt-4">
    <div class="row mb-4">
      <div class="col-md-6 offset-md-3">
        <input 
          v-model="searchQuery" 
          type="text" 
          class="form-control form-control-lg" 
          placeholder="Busque por um filme..."
        >
      </div>
    </div>

    <div id="catalog-container">
      <div v-if="isLoading" class="text-center text-light">
        <h3>Carregando...</h3>
      </div>
      <div v-else-if="error" class="text-center alert alert-danger">
        <h2>Ocorreu um Erro</h2>
        <p>{{ error }}</p>
      </div>
      <div v-else>
        <div v-for="category in categories" :key="category.title" class="movie-row">
          <h3 class="row-title">{{ category.title }}</h3>
          <div class="row-content">
            <div 
              v-for="movie in category.movies" 
              :key="movie.ids.simkl_id" 
              class="movie-card" 
              @click="openModal(movie)"
            >
              <img :src="`https://simkl.in/posters/${movie.poster}_m.jpg`" :alt="movie.title">
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="selectedMovie" class="modal-overlay" @click.self="closeModal">
      <div class="modal-content">
        <span class="modal-close" @click="closeModal">&times;</span>
        <div class="row">
          <div class="col-md-4">
            <img :src="`https://simkl.in/posters/${selectedMovie.poster}_m.jpg`" class="img-fluid rounded">
          </div>
          <div class="col-md-8">
            <h2>{{ selectedMovie.title }}</h2>
            <div v-if="isDetailLoading" class="text-light">
                <p>Carregando detalhes...</p>
            </div>
            <div v-else>
                <div v-if="selectedMovie.ratings && selectedMovie.ratings.simkl" class="mb-3">
                  <strong class="text-muted">Avaliação:</strong> 
                  <span class="badge bg-warning text-dark">{{ selectedMovie.ratings.simkl.rating }} / 10</span>
                </div>
                <hr>
                <h4>Sinopse</h4>
                <p>{{ selectedMovie.overview || 'Sinopse não disponível.' }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';

const clientId = '52be7bd6da3c3805aee6cf8bf0d6e66b267c44900613c51fa483b4cb481507c4';
const searchQuery = ref('');
const categories = ref([]);
const isLoading = ref(false);
const error = ref(null);
const selectedMovie = ref(null);
const isDetailLoading = ref(false);
let debounceTimeout = null;

const fetchFromApi = async (endpoint) => {
  const url = `https://api.simkl.com/${endpoint}`;
  const response = await fetch(url, {
    headers: { 'simkl-api-key': clientId }
  });
  if (!response.ok) throw new Error(`Erro na rede: ${response.statusText}`);
  return await response.json();
};

const loadInitialCatalog = async () => {
  isLoading.value = true;
  error.value = null;
  try {
    const [marvelRes, starWarsRes, pixarRes] = await Promise.all([
      fetchFromApi(`search/movie?q=Marvel&limit=17&extended=full`),
      fetchFromApi(`search/movie?q=Star Wars&limit=17&extended=full`),
      fetchFromApi(`search/movie?q=Pixar&limit=17&extended=full`)
    ]);
    categories.value = [
      { title: 'Marvel', movies: (marvelRes || []) },
      { title: 'Star Wars', movies: (starWarsRes || []) },
      { title: 'Pixar', movies: (pixarRes || []) },
    ];
  } catch (err) {
    console.error("Erro ao carregar o catálogo inicial:", err);
    error.value = err.message;
  } finally {
    isLoading.value = false;
  }
};

const searchForMovies = async (query) => {
  isLoading.value = true;
  error.value = null;
  try {
    const results = await fetchFromApi(`search/movie?q=${query}&limit=50&extended=full`);
    categories.value = [
      { title: `Resultados para "${query}"`, movies: (results || []) }
    ];
  } catch (err) {
    console.error(`Erro ao buscar por "${query}":`, err);
    error.value = err.message;
  } finally {
    isLoading.value = false;
  }
};

const openModal = async (movie) => {
  selectedMovie.value = movie;
  isDetailLoading.value = true;
  // jQuery
  $('body').addClass('modal-open');
  try {
    const details = await fetchFromApi(`movies/${movie.ids.simkl_id}?extended=full`);
    selectedMovie.value = { ...movie, ...details };
  } catch (err) {
    console.error("Erro ao buscar detalhes do filme:", err);
  } finally {
    isDetailLoading.value = false;
  }
};

const closeModal = () => {
  selectedMovie.value = null;
  // jQuery
  $('body').removeClass('modal-open');
};

onMounted(() => { loadInitialCatalog(); });

watch(searchQuery, (newQuery) => {
  clearTimeout(debounceTimeout);
  if (newQuery.length === 0) {
    loadInitialCatalog();
    return;
  }
  if (newQuery.length > 2) {
    debounceTimeout = setTimeout(() => { searchForMovies(newQuery); }, 500);
  }
});
</script>

<style>
body {
  background-color: #141414;
}
</style>

<style scoped>
.row-title { color: #fff; margin-bottom: 1rem; }
.row-content { display: flex; overflow-x: auto; overflow-y: hidden; padding-bottom: 1rem; }
.row-content::-webkit-scrollbar { height: 8px; }
.row-content::-webkit-scrollbar-thumb { background-color: #444; border-radius: 4px; }
.movie-card { flex: 0 0 auto; width: 180px; margin-right: 10px; cursor: pointer; transition: transform 0.2s ease-in-out; border-radius: 8px; overflow: hidden; }
.movie-card:hover { transform: scale(1.05); z-index: 10; }
.movie-card img { width: 100%; height: 100%; object-fit: cover; }
.modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.8); display: flex; justify-content: center; align-items: center; z-index: 1000; }
.modal-content { background-color: #1f1f1f; color: #fff; padding: 2rem; border-radius: 10px; width: 80%; max-width: 900px; position: relative; max-height: 80vh; overflow-y: auto; }
.modal-close { position: absolute; top: 15px; right: 20px; font-size: 2rem; cursor: pointer; color: #aaa; }
.modal-close:hover { color: #fff; }
</style>