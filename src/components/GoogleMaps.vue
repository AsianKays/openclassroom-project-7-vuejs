<template>
  <div style="height: 100%; width: 100%;">
    <div id="google-map"></div>
    <FormRestaurant :coords="coordsFromClick" :state="stateFormRestaurant"></FormRestaurant>
  </div>
</template>

<script>
  // https://stackoverflow.com/questions/6219383/google-maps-api-3-check-if-marker-is-in-view
  const GoogleMapsLoader = require('google-maps');
  import { eventBus } from "../main";
  import FormRestaurant from "./FormRestaurant";

  export default {
    name: 'GoogleMaps',
    components: {
      FormRestaurant
    },
    props: {
      restaurants: {
        type: Array,
        required: true
      }
    },
    data: () => ({
      restaurantsDisplayed: [],
      google: {},
      map: {},
      arrayMarkers: [],
      coordsFromClick: {},
      stateFormRestaurant: false
    }),
    created: async function() {
      /**
       * When created, it will initialize a Google map
       */
      await this.initGoogleMap();
      eventBus.$on('update-restaurants-displayed', (restaurantsDisplayed) => {
        this.hideAllMarkers();
        this.restaurants.forEach((restaurant, index) => {
          restaurantsDisplayed.forEach(restaurantDisplayed => {
            if (restaurant.restaurantName === restaurantDisplayed.restaurantName) {
              this.setMapOnIndex(true, index);
            }
          });
        });
      });
      eventBus.$on('check-markers-visibility', () => {
        this.emitMarkersVisible();
      });
      eventBus.$on('close-form-restaurant', () => {
        this.stateFormRestaurant = false;
      });
      eventBus.$on('add-restaurant', (newRestaurant) => {
        const position = {
          lat: newRestaurant.lat,
          lng: newRestaurant.long
        };
        this.createMarker(position, 'yellow');
      });
    },
    mounted() {
      /**
       * Update restaurantsDisplayed with data from prop restaurants (all restaurants from parent)
       */
      this.restaurantsDisplayed = this.restaurants;
    },
    methods: {
      /**
       * Create Google map in French, center the vue on user localisation, create a blue marker for the user localisation,
       * create markers for every restaurants from prop restaurants
       */
      async initGoogleMap() {
        GoogleMapsLoader.KEY = process.env.VUE_APP_APIKEY;
        GoogleMapsLoader.VERSION = '3.39';
        GoogleMapsLoader.REGION = 'fr';
        const userPosition = await this.getUserLocalisation();
        GoogleMapsLoader.load((google) => {
          this.google = google;
          this.map = new google.maps.Map(document.getElementById('google-map'), {
            zoom: 12,
            center: userPosition
          });
          this.createAllRestaurantsMarkers();
          this.createMarker(userPosition, 'blue');
          this.listenerBounds(google);
          this.listenerClick(google);
        })
      },

      /**
       * Get the user localisation and return it
       */
      getUserLocalisation() {
        return new Promise(
          (resolve) => {
            if (navigator.geolocation) {
              navigator.geolocation.getCurrentPosition(
                position => {
                  const location = {
                    lat: position.coords.latitude,
                    lng: position.coords.longitude
                  };
                  eventBus.$emit('user-location', location);
                  resolve(location);
                }
              )
            }
          }
        )
      },

      /**
       * Trigger ListRestaurants.vue and tell it to update data with the current visible markers
       */
      emitMarkersVisible() {
        eventBus.$emit('update-visible-markers', this.getVisibleMarkers());
      },

      /**
       * Create a marker  on the map
       * @param {Object} position - Contains lat and lng value
       * @param {String} color - Define the color of the marker
       */
      createMarker(position, color) {
        let url = "http://maps.google.com/mapfiles/ms/icons/";
        url += color + "-dot.png";

        const marker = new this.google.maps.Marker(
          {
            position: position,
            map: this.map,
            icon: {
              url: url
            }
          }
        );
        if (color === 'red' || color === 'yellow') {
          this.arrayMarkers.push(marker);
        }
      },

      /**
       * Create all markers for every restaurant in prop restaurants
       */
      createAllRestaurantsMarkers() {
        if (this.restaurants.length !== 0) {
          this.restaurants.forEach(restaurant => {
            const position = {
              lat: restaurant.lat,
              lng: restaurant.long
            };
            this.createMarker(position, 'red')
          })
        }
      },

      /**
       * Set all markers into the state of the parameter "boolean"
       * @param {Boolean} boolean - Determine the property 'visible' state of all the markers (true or false)
       */
      setMapOnAll(boolean) {
        this.arrayMarkers.forEach(marker => {
          marker.setVisible(boolean);
        });
      },

      /**
       * Set a specific marker, determined by the index number, to the state of the parameter "boolean"
       * @param {Boolean} boolean - Determine the property 'visible' state of all the markers (true or false)
       * @param {Number} index - Determine the specific marker in the array
       */
      setMapOnIndex(boolean, index) {
        this.arrayMarkers[index].setVisible(boolean);
      },

      /**
       * Set all markers to visible
       */
      showAllMarkers() {
        this.setMapOnAll(true);
      },

      /**
       * Set all markers to hidden
       */
      hideAllMarkers() {
        this.setMapOnAll(false);
      },

      /**
       * Return an array filled with all visible markers
       */
      getVisibleMarkers() {
        const markersVisible = [];
        this.arrayMarkers.forEach((marker, index) => {
          if (this.map.getBounds().contains(marker.getPosition()) && marker.visible) {
            markersVisible.push(index);
          }
        });
        return markersVisible;
      },

      /**
       * Triggered if bounds of the map are changing. It will call emitMarkersVisible()
       * @param {Object} google - Object which contains all Google properties and functions
       */
      listenerBounds(google) {
        google.maps.event.addListener(this.map, 'bounds_changed', () => {
          this.emitMarkersVisible();
        });
      },

      /**
       * Triggered if the user click on the map. It will open a dialog component to add new restaurant.
       * @param {Object} google - Object which contains all Google properties and functions
       */
      listenerClick(google) {
        let geocoder = google.maps.Geocoder;
        google.maps.event.addListener(this.map, 'click', (e) => {
          geocoder.prototype.geocode({
            latLng: e.latLng
          }, (results, status) => {
            if (status === google.maps.GeocoderStatus.OK) {
              if (results[0]) {
                const lat = e.latLng.lat();
                const lng = e.latLng.lng();
                this.coordsFromClick = {
                  address: results[0].formatted_address,
                  lat,
                  lng,
                };
                this.stateFormRestaurant = true;
              }
            }
          })
        });
      }
    }
  }
</script>

<style scoped lang="scss">
  #google-map {
    height: 100%;
    width: 100%;
  }
</style>
