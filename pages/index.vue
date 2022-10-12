<template>
  <div class="home">

    <!-- Hero start -->

    <Hero></Hero>

    <!-- Hero end -->

    <!-- Search start -->

    <div class="container search">
      <input @keyup.enter="$fetch" type="text" placeholder="Search" v-model.lazy="searchInput">
    </div>

    <!-- Search end -->

    <!-- Loading start -->

    <Loading v-if="$fetchState.pending"/>
     
    <!-- Loading end -->

    <!-- Movies start -->

    <div v-else class="container movies">
      
      <!--Search Movies start -->

      <div v-if="searchInput !== ''" id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in searchedMovies" :key="index">
          <div class="movie-img">
            <img v-if="movie.poster_path" :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.title">
            <img v-else title="404" src="@/assets/foto/font_funt.jpg" :alt="movie.title">

            <h4 class="review">{{movie.vote_average}}</h4>
            <div class="overview">
              <p>{{movie.overview}}</p>
            </div>
          </div>

          <div class="info">
            <h2 class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></h2>
            
            <div class="release">
              <p>
                Released:{{
                  new Date(movie.release_date).toLocaleString('en-us',{
                    month:'long',
                    day:'numeric',
                    year:'numeric',
                  })
                }}
              </p>
            </div>

            <nuxt-link class="button button-light" :to="{name: 'movies-movieid', params:{movieid : movie.id}}">
              Get More Info
            </nuxt-link>

          </div>

        </div>
      </div>

      <!--Search Movies end -->


      <!-- Now Streaming start -->

      <div v-else id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.title">

            <h4 class="review">{{movie.vote_average}}</h4>
            <div class="overview">
              <p>{{movie.overview}}</p>
            </div>
          </div>

          <div class="info">
            <h2 class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></h2>
            
            <div class="release">
              <p>
                Released: {{ 
                  new Date(movie.release_date).toLocaleString('ru-us',{
                    month:'long',
                    day:'numeric',
                    year:'numeric',
                  })
                }}
              </p>
            </div>

            <nuxt-link class="button button-light" :to="{name: 'movies-movieid', params:{movieid : movie.id}}">
              Get More Info
            </nuxt-link>

          </div>

        </div>
      </div>

      <!-- Now Streaming start -->
    </div>

    <!-- Movies end -->

  </div>
</template>

<script>
import axios from 'axios';
export default {

  head(){
    return{
      title:'Movie App - Latest Streaming Movie info',
      meta:[
        {
          hid: 'description',
          name:'description',
          content:'Get all the latest streaming mivies in theaters & online',
        },

        {
          hid: 'keywords',
          name:'keywords',
          content:'movies, strem, stremaing',
        },
      ]
    }
  },

  data(){
    return{
      movies:[],
      searchedMovies:[],
      searchInput:'',
    }
  },

  async fetch(){
    if(this.searchInput === ''){
      await this.getMovies()
      return
    }
    
    if (this.searchInput !== '') {
      await this.searchMovies()
    }
  },

  fetchDelay:1000,

  methods:{
    async getMovies() {
      const data = axios.get('https://api.themoviedb.org/3/movie/now_playing?api_key=37ed43a4f8eaa2abd75f9283692947bc&language=en-US&page=1')
      const result = await data
      result.data.results.forEach(movie =>{
        this.movies.push(movie)
      })
    },

    async searchMovies(){
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=37ed43a4f8eaa2abd75f9283692947bc&language=en-US&page=1&query=${this.searchInput}`
      )
      const result = await data
      result.data.results.forEach(movie =>{
        this.searchedMovies.push(movie)
      })
    },
  },
  watch:{
      searchInput() {
        console.log(this.searchInput)
      },
    }
}
</script>

<style lang="scss" scoped>
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
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
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
        grid-template-columns: repeat(2, 1fr);
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
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
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