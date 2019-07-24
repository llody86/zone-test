<template>
    <div class="modal">
      <div v-on:click="clickHandler" class="modal__icon-container">
        <svg class="modal__close-icon" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" xml:space="preserve" version="1.1" style="shape-rendering:geometricPrecision;text-rendering:geometricPrecision;image-rendering:optimizeQuality;" viewBox="0 0 847 847" x="0px" y="0px" fill-rule="evenodd" clip-rule="evenodd"><defs></defs><g><path class="fil0" d="M57 116c20,-20 39,-39 59,-59 16,-16 43,-16 59,0 83,83 165,166 248,249 83,-83 166,-166 249,-249 16,-16 43,-16 59,0 19,20 39,39 58,59 16,16 16,43 0,59 -83,83 -166,165 -248,248 82,83 165,166 248,249 16,16 16,43 0,59 -19,19 -39,39 -58,58 -16,16 -43,16 -59,0 -83,-83 -166,-166 -249,-248 -83,82 -165,165 -248,248 -16,16 -43,16 -59,0 -20,-19 -39,-39 -59,-58 -16,-16 -16,-43 0,-59 83,-83 166,-166 249,-249 -83,-83 -166,-165 -249,-248 -16,-16 -16,-43 0,-59z"></path></g></svg>
      </div>
      <div class="modal__info-panel">
        <div class="modal__image-container">
          <div class="modal__colour-overlay"></div>
          <img class="modal__image" v-bind:src="movieBackdrop"/>
        </div>
        <div class="modal__info-container">
          <p class="modal__text modal__text--title modal__text--bold">{{item.title}}
          <p class="modal__text">{{item.overview}}</p>
          <p class="modal__text">Release Date: <span class="modal__text--bold">{{item.release_date}}</span></p>
        </div>
      </div>
    </div>
</template>

<script>
export default {
  name: 'modal',
  props: {item: Object},
  created() {
    // eslint-disable-next-line
    // console.log(this.movies)
    // let image = document.querySelector('.modal__image');
    // image.classList.remove('modal__image--hide');
  },
  computed: {
    movieBackdrop() {
      if((this.item.backdrop_path !== undefined)){
        return `http://image.tmdb.org/t/p/w1280${this.item.backdrop_path}`
      }else{
        return ''
      }
    }
  },
  methods: {
    clickHandler: function () {
      // let image = document.querySelector('.modal__image');
      // image.classList.add('modal__image--hide');
      this.$emit('close-modal')
    }
  }
}
</script>
