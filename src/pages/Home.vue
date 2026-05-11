<script setup lang="ts">
import { ref } from 'vue';
import Loading from "@/components/Loading.vue";
import { DataService } from '@/services/data.service';

const movies = ref<any[]>([])

DataService.getMovies()
  .then(rsp => movies.value = rsp.data.sort((a, b) => b.movieId - a.movieId))
</script>

<template>

  <div class="d-flex flex-wrap justify-content-center gap-3">
    <Loading v-if="movies.length === 0"/>
    <div class="card movie-card text-center" v-for="movie in movies">

      <img :src="movie.poster" class="card-img-top" :alt="movie.title">

      <div class="card-body">
        <h5 class="card-title">{{ movie.title }}</h5>
        <h6 class="card-subtitle mb-2 text-body-secondary">{{ movie.director.name }}</h6>
      </div>

      <div class="card-footer">
        <RouterLink :to="`/details/${movie.movieId}`" class="btn btn-primary"> <i class="fa-solid fa-arrow-up-right-from-square"></i> Details</RouterLink>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .movie-card {
    transition: transform 0.3s ease;
    width: 14rem;
  }

  .movie-card:hover {
    transform: scale(1.05);
  }
</style>
