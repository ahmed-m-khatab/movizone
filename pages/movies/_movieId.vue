<template>
    <div>
        <!-- logo -->
        <div class="logo-cont">
            <NuxtLink :to="{ name: 'index' }"><img src="../../assets/imgs/logo.png" class="logo" alt="logo image"></NuxtLink>
        </div>

        <!-- Loading -->
        <Loading v-if="$fetchState.pending" />

        <!-- error state -->
        <p v-else-if="$fetchState.error">Sorry, an error occurred :(</p>
    
        <!-- Movie Info -->
        <div v-else class="single-movie container">
            <NuxtLink class="button" :to="{ name: 'index' }">&#8592;  Back To Movies </NuxtLink>
            <div class="movie-info">
                <div class="movie-img">
                    <img
                    v-if="movie.poster_path"
                    :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
                    alt=""
                    />
                    <img
                    v-else
                    src="../../assets/imgs/banner.jpg"
                    alt=""
                    />
                </div>
                <div class="movie-content">
                <h1>{{ movie.title }}</h1>
                <p class="movie-fact tagline">
                    "{{ movie.tagline }}"
                </p>
                <p class="movie-fact">
                    <span>&#128198; Released on:</span>
                    {{
                    new Date(movie.release_date).toLocaleString('en-us', {
                        month: 'long',
                        day: 'numeric',
                        year: 'numeric',
                    })
                    }}
                </p>
                <p class="movie-fact">
                    <span>&#128337; Duration:</span> {{ movie.runtime }} minutes
                </p>
                <p class="movie-fact">
                    <span>&#65284; Revenue:</span>
                    {{
                    movie.revenue.toLocaleString('en-us', {
                        style: 'currency',
                        currency: 'USD',
                    })
                    }}
                </p>
                <p class="movie-fact">{{ movie.overview }}</p>
                </div>
            </div>
        </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  
  export default {
    name: 'MovieDetails',

    data() {
      return {
        movie: {},
      }
    },
  
    async fetch() {
      await this.getSingleMovie()
    },
  
    // delay for fetching
    fetchDelay: 1000,
  
    head() {
      return {
        title: 'MovieZone | ' + this.movie.title,
      }
    },
  
    methods: {
      async getSingleMovie() {
        const data = axios.get(
          `https://api.themoviedb.org/3/movie/${this.$route.params.movieId}?api_key=1445d85775cede92406d0de393845c5d&language=en-US`
        )
        const result = await data
        this.movie = result.data
      },
    },
  }
  </script>
  
  <style lang="scss">
  .logo-cont {
    text-align: center;
    .logo {
        width: 20%;
        margin-top: 20px;
    }
  }
  .single-movie {
    color: #fff;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 32px 16px;
  
    .button {
      align-self: flex-start;
      margin-bottom: 32px;
    }
  
    .movie-info {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 32px;
      color: #fff;
      @media (min-width: 800px) {
        flex-direction: row;
        align-items: flex-start;
      }
      .movie-img {
        img {
          max-height: 400px;
          width: 100%;
  
          @media (min-width: 800px) {
            max-height: 500px;
            width: initial;
          }
        }
      }
  
      .movie-content {
        h1 {
          font-size: 56px;
          font-weight: 400;
        }
  
        .movie-fact {
          margin-top: 12px;
          font-size: 20px;
          line-height: 1.5;
  
          span {
            font-weight: 600;
          }
        }
  
        .tagline {
          font-style: italic;
          span {
            font-style: normal;
          }
        }
      }
    }
  }
  </style>