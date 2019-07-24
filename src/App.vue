<template>
  <div id="app">
    <div class="modal__container">
      <Modal v-on:close-modal='toggleModal()' v-bind:item='currentMovie'></Modal>
    </div>
    <div class="filters__container">
      <h1>Andy's Vue Movies App</h1>
      <h2>Filters for this page</h2>
      <div class="filters__genre-container">
        <div class="filters__genre-item" v-for="item in genreData" :key="item.id">
          <input type="checkbox" :id="item.name" :value="item.id" v-model="checkedGenres"/>
          <label :for="item.name">{{item.name}}</label>
        </div>
      </div>
      <div class="filters__rating-container">
        <label class="filters__text--bold" for="ratingFilter">Average Rating</label>
        <input type="range" step="0.5" min="0" max="10" id="ratingFilter" value="ratingValue" v-model="ratingValue"/>
        <p class="filters__text"><span class="filters__text--bold">{{ratingValue}}</span> /10</p>
      </div>
    </div>
    <div class="pagination__container">
      <button class="pagination__button pagination__prev" v-on:click='changePage(-1)'>&#x21e6;</button>
      <p class="filters__text filters__text--bold">Current Page: {{this.page}} / {{this.totalPages}}</p>
      <button class="pagination__button pagination__next" v-on:click='changePage(1)'>&#x21e8;</button>
    </div>
    <div class="grid__container">
      <div class="grid__list">
        <MovieCard v-on:select-movie='getMovie($event)' v-for='movie in filteredByGenreThenRating' v-bind:key='movie.id' v-bind:item="movie" v-bind:genres="getGenresForMovie(movie)"></MovieCard>
        <div v-if="filteredByGenreThenRating.length === 0">
          <h2>There don't seem to be any movies to show with those filters active!</h2>
          <p>Nobody watches THOSE kind of movies anymore, do they!?</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MovieCard from './components/MovieCard.vue'
import Modal from './components/Modal.vue'
import axios from 'axios'
import _ from 'lodash'

export default {
  name: 'vue-movies-app',
  components: {
    MovieCard, 
    Modal
  },
  data() {
    return {
      moviesData: [], //The original list of movies being passed throught from API
      ratingValue: 3, //The 'average rating' value to check against, used for filtering. Default is set to 3, increments in steps of 0.5.
      currentMovie: {}, //The current movie being inspected
      page: 1, // For pagination
      totalPages: 0, //For pagination
      genreData: [], //Used to hold the original genre list being passed by the API
      checkedGenres: [] //Contains list of currently-checked genres - this will be used to filter against.
    }
  },
  mounted() {
    this.getAllGenres(); //This will fire the "getResults" method upon successful response, which returns attempts to retrieve movies data
  },
  computed: {
    filteredByGenreThenRating() {
      if(this.checkedGenres.length > 0){
        // this.filterByGenre();
        let genreFilteredArray = [];
        genreFilteredArray = this.moviesData.filter(item => _.difference(this.checkedGenres, item.genre_ids).length === 0);
        return genreFilteredArray.filter(item => item.vote_average >= this.ratingValue).sort((a, b) => { return a.vote_average < b.vote_average; });
      }else{
        return this.moviesData.filter(item => item.vote_average >= this.ratingValue).sort((a, b) => { return a.vote_average < b.vote_average; });
      }
    },
    // filteredByGenre() {
    //   // return this.moviesData.filter(item => item.genre_ids).sort((a,b) => { return a.popularity < b.popularity; });
    // }
  },
  methods: {
    existsInCheckedArray: function(id) {
      this.checkedGenres.includes(id)
    },
    getGenresForMovie: function(movieItem){ //getting genre names for movie cards
      if((movieItem !== null) && (movieItem !== {})){
        let movieCardGenres = []
        this.genreData.forEach(element => {
          if(movieItem.genre_ids.indexOf(element.id) !== -1){
            movieCardGenres.push(element.name)
          }
        });
        return movieCardGenres;
      }
    },
    getAllGenres: function() { //attempting to retrieve API data for genres
      axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=a9a1ed5b83ef67c64d86d06690680786&language=en-US`)
        .then(response => {
          this.genreData = response.data.genres;
          this.getResults();
        })
        .catch(error => {
          // eslint-disable-next-line
          console.log(error)
        });
    },
    getResults: function() { //attempting to retrieve API data for movies
      axios.get(`https://api.themoviedb.org/3/movie/now_playing?api_key=a9a1ed5b83ef67c64d86d06690680786&language=en-US&page=${this.page}`)
        .then(response => {
          this.moviesData = response.data.results;
          this.totalPages = response.data.total_pages;
          this.checkPage();
        })
        .catch(error => {
          // eslint-disable-next-line
          console.log(error)
        });
    },
    getMovie: function(itemObj) {//Click Handler - sets currentMovie and toggles modal display
      if(itemObj != null){
        this.currentMovie = itemObj;
        this.toggleModal();
      }
    },
    toggleModal: function() {//toggle Modal visually
      let modal = document.querySelector('.modal__container');
      modal.classList.toggle('modal__container--show');
    },
    checkPage: function() { //pagination checker
      let prevBtn = document.querySelector('.pagination__prev');
      let nextBtn = document.querySelector('.pagination__next');

      if(this.page >= this.totalPages){
        //hide next btn
        nextBtn.classList.add('hide');
      }
      else if(this.page <= 1){
        //hide prev button
        prevBtn.classList.add('hide');
      }
      else{
        nextBtn.classList.remove('hide');
        prevBtn.classList.remove('hide');
      }
    },
    changePage: function(value) { //update page index and get new results based on filters
      // eslint-disable-next-line
      console.log(this.page);
      this.page += value;
      // eslint-disable-next-line
      console.log(this.page);
      this.checkPage();
      this.getResults();
    },
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
