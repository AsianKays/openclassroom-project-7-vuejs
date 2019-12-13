<template>
  <div id="app">
    <ListRestaurants></ListRestaurants>
  </div>
</template>

<script>
import ListRestaurants from './components/ListRestaurants.vue'

export default {
  name: 'app',
  components: {
    ListRestaurants
  }
}
</script>

<style>

body {
  height: 100vh;
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
}

</style>
