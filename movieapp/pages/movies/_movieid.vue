<template>
  <div>
    <v-row class="justify-center ma-10">
      <v-col cols="12" sm="4">
        <v-img
          :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
          height="auto"
        ></v-img>
      </v-col>
      <v-col cols="12" sm="3">
        <h1 class="mb-4">{{ movie.title }}</h1>

        <span class="deep-orange--text font-weight-medium">Tagline:</span>
        <p class="mb-4"> "{{ movie.tagline }}"</p>

        <span class="deep-orange--text font-weight-medium">Duration:</span>
        <p class="mb-4"> {{ movie.runtime }} minutes</p>

        <span class="deep-orange--text font-weight-medium">Overview:</span>
        <p class="my-b"> {{ movie.overview }}</p>
        <v-btn
          class="my-4"
          rounded
          dark
          medium
          outlined
          :to="{ name: 'index' }"
        >
          Go back
        </v-btn>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'singleMovie',

  async fetch() {
    await this.getSingleMovie()
  },
  fetchDelay: 1000,

  data() {
    return {
      movie: '',
    }
  },

  methods: {
    async getSingleMovie() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US`
      )
      const result = await data
      this.movie = result.data
    },
  },
}
</script>

<style>
</style>