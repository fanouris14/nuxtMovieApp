<template>
  <div>
    <v-row class="my-6 ms-2">
      <v-col cols="12" sm="3">
        <v-text-field
          label="Search.."
          outlined
          dense
          @keyup.enter="$fetch"
          v-model.lazy="searchInput"
        ></v-text-field>
      </v-col>
      <v-col cols="12" sm="3">
        <v-select
          :items="genreMovie"
          item-text="name"
          item-value="id"
          label="Search by Category"
          dense
          outlined
          v-model.lazy="genreInput"
          @change="$fetch"
        ></v-select>
      </v-col>
      <v-col cols="12" sm="3">
        <v-select
          :items="sortBy"
          label="Sort by.."
          dense
          outlined
          v-model.lazy="sortByInput"
          @change="selectSort()"
        ></v-select>
      </v-col>

      <v-col class="text-md-right" cols="12" sm="3">
        <v-btn
          class="ma-1"
          outlined
          color="red"
          v-show="
            searchInput !== '' || genreInput !== null || sortByInput !== ''
          "
          @click="clearSearch"
        >
          Clear Results
        </v-btn>
      </v-col>
    </v-row>

    <div class="container movies">
      <!-- Searched Movies -->
      <div
        id="movie-grid"
        v-if="searchInput !== '' && genreInput === null"
        class="movies-grid pt-1"
      >
        <v-layout row wrap>
          <v-flex
            xs12
            sm6
            md4
            lg3
            v-for="(movie, index) in searchedMovies"
            :key="index"
          >
            <v-card class="ma-6">
              <v-img
                :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
                height="auto"
              ></v-img>

              <v-card-title>
                {{ movie.title.slice(0, 25)
                }}<span v-if="movie.title.length > 25">...</span>
              </v-card-title>

              <v-card-subtitle>
                Released:
                {{
                  new Date(movie.release_date).toLocaleString('en-us', {
                    month: 'long',
                    day: 'numeric',
                    year: 'numeric',
                  })
                }}
              </v-card-subtitle>

              <v-card-actions>
                <v-btn color="orange lighten-2" text>
                  rating: {{ movie.vote_average }}
                </v-btn>

                <v-spacer></v-spacer>

                <v-btn
                  rounded
                  dark
                  small
                  outlined
                  :to="{ name: 'movies-movieid', params: { id: movie.id } }"
                >
                  More Info
                </v-btn>

                <v-btn icon @click="selectedIndex = movie">
                  <v-icon>{{
                    movie === selectedIndex
                      ? 'mdi-chevron-up'
                      : 'mdi-chevron-down'
                  }}</v-icon>
                </v-btn>
              </v-card-actions>

              <v-expand-transition>
                <div v-show="movie === selectedIndex">
                  <v-divider></v-divider>

                  <v-card-text>
                    {{ movie.overview }}
                  </v-card-text>
                </div>
              </v-expand-transition>
            </v-card>
          </v-flex>
        </v-layout>
      </div>

      <!-- Genre Movies -->
      <div
        id="movie-grid"
        v-if="genreInput !== null && searchInput === ''"
        class="movies-grid pt-1"
      >
        <v-pagination
          v-model.lazy="page"
          :length="pagesNum"
          :total-visible="8"
          @input="$fetch"
        ></v-pagination>
        <v-layout row wrap>
          <v-flex
            xs12
            sm6
            md4
            lg3
            v-for="(movie, index) in genreMovies"
            :key="index"
          >
            <v-card class="ma-6">
              <v-img
                :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
                height="auto"
              ></v-img>

              <v-card-title>
                {{ movie.title.slice(0, 25)
                }}<span v-if="movie.title.length > 25">...</span>
              </v-card-title>

              <v-card-subtitle>
                Released:
                {{
                  new Date(movie.release_date).toLocaleString('en-us', {
                    month: 'long',
                    day: 'numeric',
                    year: 'numeric',
                  })
                }}
              </v-card-subtitle>

              <v-card-actions>
                <v-btn color="orange lighten-2" text>
                  rating: {{ movie.vote_average }}
                </v-btn>

                <v-spacer></v-spacer>

                <v-btn
                  rounded
                  dark
                  small
                  outlined
                  :to="{ name: 'movies-movieid', params: { id: movie.id } }"
                >
                  More Info
                </v-btn>

                <v-btn icon @click="selectedIndex = movie">
                  <v-icon>{{
                    movie === selectedIndex
                      ? 'mdi-chevron-up'
                      : 'mdi-chevron-down'
                  }}</v-icon>
                </v-btn>
              </v-card-actions>

              <v-expand-transition>
                <div v-show="movie === selectedIndex">
                  <v-divider></v-divider>

                  <v-card-text>
                    {{ movie.overview }}
                  </v-card-text>
                </div>
              </v-expand-transition>
            </v-card>
          </v-flex>
        </v-layout>
        <v-pagination
          v-model.lazy="page"
          :length="pagesNum"
          :total-visible="8"
          @input="$fetch"
        ></v-pagination>
      </div>

      <!-- Default view Movies -->
      <div
        id="movie-grid"
        v-if="genreInput === null && searchInput === ''"
        class="movies-grid pt-1"
      >
        <v-pagination
          v-model.lazy="page"
          :length="pagesNum"
          :total-visible="8"
          @input="$fetch"
        ></v-pagination>
        <v-layout row wrap>
          <v-flex
            xs12
            sm6
            md4
            lg3
            v-for="(movie, index) in movies"
            :key="index"
          >
            <v-card class="ma-6">
              <v-img
                :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
                height="auto"
              ></v-img>

              <v-card-title>
                {{ movie.title.slice(0, 25)
                }}<span v-if="movie.title.length > 25">...</span>
              </v-card-title>

              <v-card-subtitle>
                Released:
                {{
                  new Date(movie.release_date).toLocaleString('en-us', {
                    month: 'long',
                    day: 'numeric',
                    year: 'numeric',
                  })
                }}
              </v-card-subtitle>

              <v-card-actions>
                <v-btn color="orange lighten-2" text>
                  rating: {{ movie.vote_average }}
                </v-btn>

                <v-spacer></v-spacer>
                <v-btn
                  rounded
                  dark
                  small
                  outlined
                  :to="{ name: 'movies-movieid', params: { id: movie.id } }"
                >
                  More Info
                </v-btn>

                <v-btn icon outlined small @click="selectedIndex = movie">
                  <v-icon>{{
                    movie === selectedIndex
                      ? 'mdi-chevron-up'
                      : 'mdi-chevron-down'
                  }}</v-icon>
                </v-btn>
              </v-card-actions>

              <v-expand-transition>
                <div v-show="movie === selectedIndex">
                  <v-divider></v-divider>

                  <v-card-text>
                    {{ movie.overview }}
                  </v-card-text>
                </div>
              </v-expand-transition>
            </v-card>
          </v-flex>
        </v-layout>
        <v-pagination
          v-model.lazy="page"
          :length="pagesNum"
          :total-visible="8"
          @input="$fetch"
        ></v-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data: () => ({
    show: false,
    movies: [],
    searchedMovies: [],
    genreMovie: [],
    genreMovies: [],
    searchInput: '',
    selectedIndex: null,
    genreInput: null,
    page: 1,
    pagesNum: null,
    sortBy: ['Alphabetical Order', 'Rating (higher to lower)'],
    sortByInput: '',
  }),

  //fetch
  async fetch() {
    if (this.searchInput === '' && this.genreInput === null) {
      await this.getMovies()
      await this.getMoviesPages()
      await this.genreList()
      return
    }
    if (this.searchInput !== '') {
      await this.searchMovies()
    }
    if (this.genreInput !== null) {
      await this.genreMoviesFeed()
      await this.genreMoviesFeedPages()
    }
  },
  fetchDelay: 1000,

  methods: {
    //get movie pages number
    async getMoviesPages() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US`
      )
      const result = await data
      if (result.data.total_pages > 500) {
        //condition due to API restrictions
        this.pagesNum = 500
      } else {
        this.pagesNum = result.data.total_pages
      }
    },

    //genre movie list feed pages number
    async genreMoviesFeedPages() {
      const data = axios.get(
        `https://api.themoviedb.org/3/discover/movie?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US&sort_by=popularity.desc&with_genres=${this.genreInput}`
      )
      const result = await data
      if (result.data.total_pages > 500) {
        //condition due to API restrictions
        this.pagesNum = 500
      } else {
        this.pagesNum = result.data.total_pages
      }
    },

    //get movie function
    async getMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US&page=${this.page}`
      )
      this.movies = []
      const result = await data
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
    },

    //search movie function
    async searchMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US&page=1&query=${this.searchInput}`
      )
      this.searchedMovies = []
      const result = await data
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
    },

    //get movies genre list
    async genreList() {
      const data = axios.get(
        `https://api.themoviedb.org/3/genre/movie/list?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US`
      )
      const result = await data
      result.data.genres.forEach((movie) => {
        this.genreMovie.push(movie)
      })
    },

    //genre movie list feed
    async genreMoviesFeed() {
      const data = axios.get(
        `https://api.themoviedb.org/3/discover/movie?api_key=d491d7e7e3ff91635356dc09776e24d5&language=en-US&sort_by=popularity.desc&with_genres=${this.genreInput}&page=${this.page}`
      )
      this.genreMovies = []
      const result = await data
      result.data.results.forEach((movie) => {
        this.genreMovies.push(movie)
      })
    },

    //clear results
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
      this.genreInput = null
      this.genreMovies = []
      this.page = 1
      this.pagesNum = null
      this.getMovies()
      this.getMoviesPages()
      this.sortByInput = ''
    },

    //select init
    selectSort() {
      if (this.sortByInput === 'Alphabetical Order') {
        if (this.movies.length) {
          this.movies.sort(this.compareNames)
        }
        if (this.searchedMovies.length) {
          this.searchedMovies.sort(this.compareNames)
        }
        if (this.genreMovies.length) {
          this.genreMovies.sort(this.compareNames)
        }
      } else if (this.sortByInput === 'Rating (higher to lower)') {
        if (this.movies.length) {
          this.movies.sort(this.compareRatings)
        }
        if (this.searchedMovies.length) {
          this.searchedMovies.sort(this.compareRatings)
        }
        if (this.genreMovies.length) {
          this.genreMovies.sort(this.compareRatings)
        }
      }
    },

    //sort by movie name
    compareNames(a, b) {
      if (a.title < b.title) {
        return -1
      }
      if (a.title > b.title) {
        return 1
      }
      return 0
    },

    //sort by movie rating (top to low)
    compareRatings(a, b) {
      if (a.vote_average > b.vote_average) {
        return -1
      }
      if (a.vote_average < b.vote_average) {
        return 1
      }
      return 0
    },
  },
}
</script>



