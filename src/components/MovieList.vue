<template>
  <div class="container">
    <div class="nav">
      <button @click="imdbAsc()">Lowest rated</button> -
      <button @click="imdbDesc()">Highest rated</button>
    </div>
    <ul>
      <li>
          <strong>Movie name</strong> <strong><span>Movie rating</span></strong>
      </li>
      <li v-for="(movie, index) in getMovieList" :key="index" v-if="index < 15">
          {{ movie.title }} <span>{{ movie.imdb }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'MovieList',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      sortType: '',
      movies: []
    }
  },
  async mounted () {
    let self = this

    let response = await fetch('https://api.themoviedb.org/3/discover/movie?api_key=d993bdf37f8ab7c574c990434a85a69f&sort_by=popularity.desc')
    let responseJson = await response.json()
    let movieList = responseJson.results

    self.movies = await Promise.all(movieList.map(async i => {
      let movieName = i.title

      response = await fetch(`http://www.omdbapi.com/?t=${movieName}&apikey=45eb48de`)
      responseJson = await response.json()

      let imdb = parseFloat(responseJson.imdbRating)
      i.imdb = isNaN(imdb) ? 0 : imdb
      return i
    }))
  },
  methods: {
    imdbAsc () {
      this.sortType = 'asc'
    },
    imdbDesc () {
      this.sortType = 'desc'
    },
    compare (a, b) {
      if (this.sortType === 'asc') {
        return a.imdb - b.imdb
      }
      return b.imdb - a.imdb
    }
  },
  computed: {
    getMovieList () {
      let temp = this.movies
      if (this.sortType !== '') {
        return temp.sort(this.compare)
      }
      return temp
    }
  }
}
</script>

<style>
</style>
