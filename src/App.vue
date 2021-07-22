<template>
  <div id="app">
    <Gallery />
  </div>
</template>

<script>
import Gallery from './components/Gallery.vue'

export default {
  name: 'App',
  components: {
    Gallery
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 25px auto;
}
</style>
