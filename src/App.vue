<template>
  <div id="app">
    <ListRestaurants id="listRestaurants" :restaurants=restaurants></ListRestaurants>
    <GoogleMaps :restaurants=restaurants></GoogleMaps>
  </div>
</template>

<script>
import ListRestaurants from './components/ListRestaurants.vue'
import GoogleMaps from './components/GoogleMaps.vue'
import jsonRestaurants from './json/restaurants.json'

export default {
  name: 'app',
  components: {
    ListRestaurants,
    GoogleMaps
  },
  data: () => ({
    restaurants: jsonRestaurants,
  }) 
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
  display: flex;
}

#listRestaurants {
  width: 40%;
}

</style>
