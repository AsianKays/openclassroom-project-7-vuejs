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
  import {eventBus} from "./main";
  import axios from "axios";

  export default {
    name: 'app',
    components: {
      ListRestaurants,
      GoogleMaps
    },
    data: () => ({
      restaurants: jsonRestaurants,
      proxyUrl: 'https://cors-anywhere.herokuapp.com/',
      urlGooglePlacesApi: 'https://maps.googleapis.com/maps/api/place/nearbysearch/json?',
      location: {},
      radius: '5000'
    }),
    created: function() {
      eventBus.$on('user-location', (userLocation) => {
        this.location = userLocation;
        this.getRestaurantsFromGooglePlacesApi();
      });
    },
    methods: {
      /**
       * Triggered when app is starting. It will get existing restaurants from Google Places API.
       */
      getRestaurantsFromGooglePlacesApi() {
        const urlGooglePlacesApi = `${this.urlGooglePlacesApi}location=${this.location.lat},${this.location.lng}&radius=${this.radius}&key=${process.env.VUE_APP_APIKEY}&type=restaurant`;
        axios.get(this.proxyUrl +urlGooglePlacesApi)
            .then(response => {
              if(response.status === 200) {
                let restaurantsFromApi = response.data.results;
                restaurantsFromApi = this.adaptFormat(restaurantsFromApi);
                this.restaurants = this.restaurants.concat(restaurantsFromApi);
              }
            }).catch(error => {
              console.error(error)
            });
      },

      /**
       * Transform data from an existing array get from Google Places API to a new structure which will match
       * with the local JSON
       * @param array
       */
      adaptFormat(array) {
        let result = [];
        array.forEach(element => {
          const obj = {
            restaurantName: element.name,
            address: element.vicinity,
            lat: element.geometry.location.lat,
            long: element.geometry.location.lng,
            ratings: []
          };
          result.push(obj);
        });
        return result;
      }
    }
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

  .clickable:hover {
    cursor: pointer;
  }
</style>
