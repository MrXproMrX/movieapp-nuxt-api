<template>
    <section>
        <!-- Loading start -->
        
        <Loading v-if="$fetchState.pending"/>
        
        <!-- Loading end -->

        <div v-else class="container single-movie">
            <nuxt-link class="button" :to="{name: 'index'}">Back</nuxt-link>
            <div class="movie-info">
                <div class="movie-img">
                    <img v-if="movie.poster_path" :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.title">
                    <img v-else src="@/assets/foto/font_funt.jpg" title="404" :alt="movie.title">
                </div>

                <div class="movie-content">
                    <h1>Title: {{ movie.title }}</h1>
                    <div class="movie-fact tagline">
                        <span>Tagline: </span>
                        <p>{{movie.tagline}}</p>
                    </div>

                    <div class="movie-fact">
                        <span>Released:</span>  {{ 
                            new Date(movie.release_date).toLocaleString('en-us',{
                            month:'long',
                            day:'numeric',
                            year:'numeric',
                            })
                        }}
                    </div>

                    <div class="movie-fact">
                        <span>Duration:</span> {{ movie.runtime }} minutes
                    </div>

                    <div class="movie-fact">
                        <span>Revenue:</span>
                        {{
                            movie.revenue.toLocaleString('en-us',{
                                style:'currency',
                                currency:'USD',
                            })
                        }}
                    </div>

                    <div class="movie-fact">
                        <span>Overflow:</span>
                        <p>{{ movie.overview }}</p>
                    </div>

                </div>
            </div>
        </div>

    </section>
</template>


<script>
import axios from 'axios';
export default {
    name: 'sengle-movie',
    head(){
        return{
            title: this.movie.title,
        }
    },
    data(){
        return{
            movie:'',
        }
    },

    async fetch(){
        await this.getSingLeMovie()
    },
    fetchDelay:1000,

    methods:{
        async getSingLeMovie(){
            const data = axios.get(
                `https://api.themoviedb.org/3/movie/${this.$route.params.movieid}?api_key=37ed43a4f8eaa2abd75f9283692947bc&language=en-US`
            )
            const result = await data
            this.movie = result.data
        }
    },
    
}
</script>

<style lang="scss">
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
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
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
          text-decoration: underline;
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