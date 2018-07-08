<template>
  <div id="app">
    <h1>{{ title }}</h1>
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      title: 'IMDB movies:'
    }
  }
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Source+Serif+Pro';
</style>
