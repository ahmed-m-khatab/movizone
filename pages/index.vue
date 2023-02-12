<template>
  <div class="home">
    <!-- Hero -->
    <hero-section></hero-section>

     <!-- Search -->
    <div class="container search">
      <input
        v-model="searchInput"
        type="text"
        placeholder="Search"
        @keyup.enter="$fetch"
      />
      <button v-show="searchInput !== ''" class="button-white" @click="clearSearch">
        &#10006;
      </button>
      <button class="button" @click="$fetch">
        Search
      </button>
    </div>

    <!-- loading spinner -->
    <loading-spinner v-if="$fetchState.pending" />

    <!-- error state -->
    <p v-else-if="$fetchState.error">Sorry, an error occurred :(</p>

    <!-- movies -->
    <div v-else id="movies-grid" class="container movies">
      <!-- exit search results -->
      <button v-show="searchedMovies.length" class="button-text" @click="clearSearch">
        &#8592; bACK TO STREAMING 
      </button>
      
      <!-- streaming movies -->
      <div v-if="!searchedMovies.length" class="movies-grid">
        <div v-for="(item, i) in movies" :key="i" class="movie">
          <div class="movie-img">
            <img
              v-if="item.poster_path"
              :src="`https://image.tmdb.org/t/p/w500/${item.poster_path}`"
              alt=""
            />
            <img
              v-else
              src="../assets/imgs/banner.jpg"
              alt=""
            />
            <p class="review">{{ item.vote_average }}</p>
            <p :class="item.overview.length ? 'overview' : ''">{{ item.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ item.title.slice(0, 25) }}
              <span v-if="item.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(item.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <NuxtLink class="button button-light" :to="{ name: 'movies-movieId', params: { movieId: item.id } }">
              View Details
            </NuxtLink>
          </div>
        </div>
      </div>

      <!-- searched movies -->
      <div v-else class="movies-grid">
        <div v-for="(item, i) in searchedMovies" :key="i" class="movie">
          <div class="movie-img">
            <img
              v-if="item.poster_path"
              :src="`https://image.tmdb.org/t/p/w500/${item.poster_path}`"
              alt=""
            />
            <img
              v-else
              src="../assets/imgs/banner.jpg"
              alt=""
            />
            <p class="review">{{ item.vote_average }}</p>
            <p class="overview">{{ item.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ item.title.slice(0, 25) }}
              <span v-if="item.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(item.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <NuxtLink class="button button-light" :to="{ name: 'movies-movieId', params: { movieId: item.id } }">
              View Details
            </NuxtLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HomePage',
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
    }
  },
  
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }
    await this.searchMovies()
  },

  head() {
    return {
      title: 'MovieZone | Every thing you need to know about current movies',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest streaming movies in theaters & online',
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, stremaing, box Office',
        },
      ],
    }
  },
  
  fetchDelay: 1000,
  // watch: {
  //   searchInput() {
  //     console.log(this.searchInput)
  //   },
  // },
  methods: {
    async getMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=1445d85775cede92406d0de393845c5d&language=en-US&page=1`
      )
      const result = await data
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
    },
    async searchMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=1445d85775cede92406d0de393845c5d&language=en-US&page=1&query=${this.searchInput}`
      )
      const result = await data
      this.searchedMovies = []
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
    },
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
    },
  },
}
</script>

<style lang="scss">
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      border-top-left-radius: 5px;
      border-bottom-left-radius: 5px;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .button-text {
    margin-bottom: 20px;
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(3, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #9b24fc;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.4),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: hsla(273, 97%, 56%, 0.966);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
          }
        }
      }
    }
  }
}
</style>