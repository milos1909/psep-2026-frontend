<script setup lang="ts">
import { computed, ref } from 'vue'
import { useRoute } from 'vue-router'
import Loading from '@/components/Loading.vue'
import type { MovieModel } from '@/models/movie.model'
import { DataService } from '@/services/data.service'

const route = useRoute()
const id = Number(route.params.id)

const movie = ref<MovieModel>()
const loading = ref(true)
const error = ref<string | null>(null)

const genres = computed(() => {
  return movie.value?.movieGenres.map((movieGenre) => movieGenre.genre.name).join(', ') || 'No genres available'
})

const actors = computed(() => {
  return movie.value?.movieActors.map((movieActor) => movieActor.actor.name) || []
})

const formatDate = (date: string) => {
  return new Intl.DateTimeFormat('en', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }).format(new Date(date))
}

DataService.getMovieById(id)
  .then((rsp) => {
    movie.value = rsp.data
    document.title = `${rsp.data.title} | PSEP 2026`
  })
  .catch(() => {
    error.value = 'Movie details could not be loaded.'
  })
  .finally(() => {
    loading.value = false
  })
</script>

<template>
  <main class="container py-5">
    <Loading v-if="loading" />

    <div v-else-if="error" class="alert alert-danger text-center">
      {{ error }}
    </div>

    <section v-else-if="movie" class="card border-0 shadow movie-details">
      <div class="row g-0">
        <div class="col-md-4">
          <img
              :src="movie.poster"
              :alt="movie.title"
              class="movie-poster"
          />
        </div>

        <div class="col-md-8">
          <div class="card-body p-4">
            <div class="d-flex flex-wrap gap-2 mb-3">
              <span class="badge text-bg-primary">
                {{ movie.runTime }} min
              </span>

              <span
                  class="badge"
                  :class="movie.active ? 'text-bg-success' : 'text-bg-secondary'"
              >
                {{ movie.active ? 'Active' : 'Inactive' }}
              </span>

              <span class="badge text-bg-light border">
                {{ formatDate(movie.startDate) }}
              </span>
            </div>

            <h1 class="card-title mb-2">
              {{ movie.title }}
            </h1>

            <h2
                v-if="movie.originalTitle && movie.originalTitle !== movie.title"
                class="h5 text-body-secondary mb-4"
            >
              {{ movie.originalTitle }}
            </h2>

            <p class="lead">
              {{ movie.shortDescription }}
            </p>

            <hr />

            <h3 class="h5">Description</h3>
            <p class="text-body-secondary">
              {{ movie.description }}
            </p>

            <div class="row mt-4">
              <div class="col-md-6 mb-3">
                <h3 class="h6 text-uppercase text-body-secondary">
                  Director
                </h3>
                <p class="mb-0 fw-semibold">
                  {{ movie.director.name }}
                </p>
              </div>

              <div class="col-md-6 mb-3">
                <h3 class="h6 text-uppercase text-body-secondary">
                  Genres
                </h3>
                <p class="mb-0">
                  {{ genres }}
                </p>
              </div>
            </div>

            <div class="mt-3">
              <h3 class="h6 text-uppercase text-body-secondary">
                Actors
              </h3>

              <div v-if="actors.length" class="d-flex flex-wrap gap-2">
                <span
                    v-for="actor in actors"
                    :key="actor"
                    class="badge rounded-pill text-bg-light border actor-badge"
                >
                  {{ actor }}
                </span>
              </div>

              <p v-else class="text-body-secondary mb-0">
                No actors available.
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">Cinema</th>
          <th scope="col">Start Time</th>
          <th scope="col">Price</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="tt in movie.timeTables">
          <th scope="row">{{ tt.cinema.name }}</th>
          <td>{{ tt.startTime }}</td>
          <td>{{ tt.price }} RSD</td>
        </tr>
      </tbody>
    </table>
  </main>
</template>

<style scoped>
.movie-details {
  overflow: hidden;
}

.movie-poster {
  width: 100%;
  height: 100%;
  min-height: 520px;
  object-fit: cover;
  display: block;
}

.actor-badge {
  font-size: 0.9rem;
  padding: 0.55rem 0.75rem;
}

.details-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 1rem;
}

.details-grid div {
  display: flex;
  flex-direction: column;
}

@media (max-width: 767.98px) {
  .movie-poster {
    min-height: auto;
  }

  .details-grid {
    grid-template-columns: 1fr;
  }
}
</style>