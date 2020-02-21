<template>
  <div id="app">
    <ListRestaurants id="listRestaurants" :restaurants=restaurants></ListRestaurants>
    <GoogleMaps :restaurants=restaurants></GoogleMaps>
  </div>
</template>

<script>
import ListRestaurants from './components/ListRestaurants.vue';
import GoogleMaps from './components/GoogleMaps.vue';
import jsonRestaurants from './json/restaurants.json';

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

<style lang="scss">
@import "~vue-material/dist/theme/engine"; // Import the theme engine
@include md-register-theme("default", (
  primary: #42b883, // The primary color of your application
  // accent: md-get-palette-color(white, 500), // The accent or secondary color
  accent: #35495e, // The accent or secondary color
  theme: dark // This can be dark or light
));
@import "~vue-material/dist/theme/all"; // Apply the theme

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
  color: #ffffff;
  height: 100%;
  display: flex;
}

#listRestaurants {
  width: 30%;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
}
</style>
