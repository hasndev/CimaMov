<template>
  <!-- Hero Section -->
  <div class="bg-red-800 py-20 text-white">
    <div class="container mx-auto px-4 text-center">
      <h1 class="text-4xl font-bold mb-4">Welcome To CimaMov</h1>
      <p class="text-lg">Millions of movies, TV shows and people to discover. Explore now.</p>
      <div class="mt-8">
        <input type="text" placeholder="Search movies"
          class="max-w-4xl text-center w-full px-4 py-2 text-gray-700 bg-white border border-gray-300 rounded shadow-sm focus:outline-none focus:border-red-600"
          v-model="searchQuery" @input="searchMovies">
      </div>
    </div>
  </div>

  <!-- Movie Section -->
  <div class="bg-gray-900 py-16">
    <div class="container mx-auto px-4">
      <h2 v-if="!searchQuery" class="text-3xl font-bold mb-8 text-center text-red">Trending</h2>
      <h2 v-else class="text-3xl font-bold mb-8 text-center text-red">Results Search About "{{ searchQuery
      }}"</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8">
        <!-- Movie Card -->
        <Movie v-for="movie in movies" :movie="movie" :key="movie.id"></Movie>
      </div>
    </div>
  </div>

  <!-- Movie Section -->
  <div class="bg-gray-900 py-16">
    <div class="container mx-auto px-4">
      <h2 class="text-3xl font-bold mb-8 text-center text-red">Top Rated</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8">
        <!-- Movie Card -->
        <Movie v-for="movie in moviesPopular" :movie="movie" :key="movie.id"></Movie>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted, computed } from "vue";
import Movie from './components/Movie.vue';

const movies = ref([]);
const moviesPopular = ref([]);
const searchQuery = ref('');
const header = {
  Authorization: "Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJmNjYwOGQzNjM4OTU1MmRmMWUzNzU3YWQxYmI5NDM1YyIsInN1YiI6IjY0NjY0NzQxYTUwNDZlMDE0NzRiY2RjOCIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.cyuO_J2UjXn8kBP1E9GksynNa-rcMOjpUKS-UppNy7U"
};

const fetchMovies = async () => {
  const response = await axios.get('https://api.themoviedb.org/3/trending/movie/day', {
    headers: header
  });
  movies.value = response.data.results.slice(0, 8);
}

const searchMovies = async () => {
  const searchResponse = await axios.get('https://api.themoviedb.org/3/search/movie', {
    headers: header,
    params: {
      query: searchQuery.value
    }
  });
  movies.value = searchResponse.data.results;
}

const filteredMovies = computed(() => {
  if (!searchQuery.value) {
    return fetchMovies();
  }
  return searchMovies();
});


const fetchMoviesPopular = async () => {
  const response = await axios.get('https://api.themoviedb.org/3/movie/top_rated/', {
    headers: header
  });
  moviesPopular.value = response.data.results;
}

onMounted(() => {
  fetchMovies();
  fetchMoviesPopular();
});
</script>
