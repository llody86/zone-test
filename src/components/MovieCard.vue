<template>
    <li v-on:click="$emit('select-movie', item)" class="grid-item box-shadow">
        <div class="grid-item__image-container">
          <div class="grid-item__colour-overlay"></div>
          <img class="grid-item__image" data-object-fit v-bind:src="movieThumbnail"/>
        </div>
        <div class="grid-item__info-container">
          <p class="grid-item__text grid-item__text--bold grid-item__text--title">{{item.title}}</p>
          <ul class="grid-item__genre-list">
            <li class="grid-item__text grid-item__text--genre" v-for="genre in genres" :key="genre.id">{{genre}}</li>
          </ul>
        </div>
    </li>
</template>

<script>
export default {
  name: 'MovieCard',
  props: {
    item: Object,
    genres: Array
  },
  computed: {
      movieThumbnail() {
         return `http://image.tmdb.org/t/p/w780${this.item.poster_path}`
      }

  }
}
</script>
