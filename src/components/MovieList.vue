<template>
  <div class="container">
    <div class="nav">
      <button @click="imdbAsc()" :class="this.sortType === 'asc' ? 'active' : '' ">Lowest rated</button> -
      <button @click="imdbDesc()" :class="this.sortType === 'desc' ? 'active' : '' ">Highest rated</button>
    </div>
    <ul>
      <li>
          <strong><button @click="toggleSortByName()">Movie name</button></strong> <strong><span>Movie rating</span></strong>
      </li>
      <li v-for="(movie, index) in getMovieList" :key="index" v-if="index < 15" :style="`background: rgb(29, 106, 150, ${movie.imdb / 10});`">
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
      toggleNameFlag: true,
      movies: []
    }
  },
  async mounted () {
    let self = this

    let response = await fetch('https://api.themoviedb.org/3/discover/movie?api_key=d993bdf37f8ab7c574c990434a85a69f&sort_by=popularity.desc')
    let responseJson = await response.json()
    let movieList = responseJson.results

    Promise.all(movieList.map(async i => {
      let movieName = i.title

      response = await fetch(`http://www.omdbapi.com/?t=${movieName}&apikey=45eb48de`)
      responseJson = await response.json()

      let imdb = parseFloat(responseJson.imdbRating)
      i.imdb = isNaN(imdb) ? 0 : imdb
      return i
    })).then(list => {
      self.movies = list.splice(0, 15)
    })
  },
  methods: {
    imdbAsc () {
      this.sortType = 'asc'
    },
    imdbDesc () {
      this.sortType = 'desc'
    },
    toggleSortByName () {
      this.toggleNameFlag = !this.toggleNameFlag
      this.sortType = ''
    },
    compare (a, b) {
      if (this.sortType === 'asc') {
        return a.imdb - b.imdb
      }
      return b.imdb - a.imdb
    },
    compareName (a, b) {
      if (a.title < b.title) {
        return this.toggleNameFlag ? -1 : 1
      }
      if (a.title > b.title) {
        return this.toggleNameFlag ? 1 : -1
      }
      return 0
    }
  },
  computed: {
    getMovieList () {
      let temp = this.movies
      if (this.sortType !== '') {
        return temp.sort(this.compare)
      }
      return temp.sort(this.compareName)
    }
  }
}
</script>

<style>
</style>
