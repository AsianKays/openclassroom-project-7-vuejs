<template>
  <div class="md-layout" style="overflow-y: auto">
    <div class="md-layout-item">
      <h1>Geeglo Places</h1>
      <md-divider></md-divider>
      <md-field>
        <label>Rechercher un restaurant</label>
        <md-input v-model="searchedRestaurant" @input=logSearchedRestaurant></md-input>
      </md-field>

      <md-menu md-size="auto" style="margin-bottom: 10px">
        <md-chip class="md-primary chips" md-clickable md-menu-trigger>
          {{ displayedRate }}
          <md-icon>keyboard_arrow_down</md-icon>
        </md-chip>

        <md-menu-content>
          <md-menu-item @click="setRateFilter(0)" >
            <div>
              Toutes les notes
            </div>
          </md-menu-item>
          <md-menu-item @click="setRateFilter(item.nbStars.length)" v-for="(item, index) in starsForFilterOptions" :key="index">
            <div>
              <icon-star v-for="(star, index) in item.nbStars" :key="index">{{star}}</icon-star>
            </div>
          </md-menu-item>
        </md-menu-content>
      </md-menu>

      <div v-for="(restaurant, index) in restaurantsDisplayed" :key="index">
        <CardRestaurant v-bind:restaurant.sync=restaurant></CardRestaurant>
      </div>
    </div>
  </div>
</template>

<script>
  // https://medium.com/devmarketer/how-to-add-conditional-statements-to-v-for-loops-in-vue-js-c0b4d17e7dfd
  import CardRestaurant from './CardRestaurant.vue';
  import { eventBus } from "../main";
  import IconStar from './icons/IconStar.vue';

  export default {
    name: 'ListRestaurants',
    components: {
      CardRestaurant,
      IconStar
    },
    props: {
      restaurants: {
        type: Array,
        required: true
      }
    },
    data: () => ({
      searchedRestaurant: '',
      restaurantsDisplayed: [],
      displayedRate: 'Note',
      filteredRate: 0,
      starsForFilterOptions: [
        {
          nbStars: ['*','*']
        }
        ,{
          nbStars: ['*','*','*']
        }
        ,{
          nbStars: ['*','*','*','*']
        }
        ,{
          nbStars: ['*','*','*','*','*']
        }
      ]
    }),
    created: function() {
      /**
       * When created, it have to wait a trigger from GoogleMaps.vue.
       * It will get an array _markersVisible with all markers visible from the map.
       */
      eventBus.$on('update-visible-markers', (markersVisible) => {
        const restaurantsVisible = [];
        markersVisible.forEach((index) => {
          restaurantsVisible.push(this.restaurants[index])
        });
        this.restaurantsDisplayed = restaurantsVisible;
        console.log(this.restaurantsDisplayed);
      });
      eventBus.$on('add-restaurant', (newRestaurant) => {
        this.restaurants.push(newRestaurant);
        this.restaurantsDisplayed.push(newRestaurant);
      });
    },
    methods: {
      /**
       * Function semi generic for filter an array which includes the string given in third parameter
       * This value is searched in the field, given in the second parameter, of the element
       * @param {Array} arrayToFilter - Array to filter
       * @param {String} field - Key of the object
       * @param {String} searchedValue - Value we are looking for
       */
      filterGivenArray(arrayToFilter, field, searchedValue) {
        this.restaurantsDisplayed = arrayToFilter.filter(restaurant =>
          restaurant[field].toLowerCase().includes(searchedValue.toLowerCase())
        );
      },

      /**
       * Trigger GoogleMaps.vue and tell it to update the visibility of current markers on the map
       */
      displayedRestaurantsVisible() {
        eventBus.$emit('update-restaurants-displayed', this.restaurantsDisplayed);
      },

      /**
       * Trigger GoogleMaps.vue for asking to the component to return all current markers visible
       */
      checkMarkersVisibility() {
        eventBus.$emit('check-markers-visibility');
      },

      /**
       * Update displayedRate to match with the current rate filter
       * @param {Number} rate - Rate chosen to display
       */
      changeDisplayedRate(rate) {
        rate !== 0 ? this.displayedRate = `${rate}+` : this.displayedRate = 'Note';
      },

      /**
       * Filter restaurantsDisplayed by the average rate
       * @param {Number} rate - Rate chosen for the filter
       */
      setRateFilter(rate) {
        this.filteredRate = rate;
        const restaurantsFromRateFilter = this.restaurants.filter(restaurant => {
          let total = 0;
          const ratings = restaurant['ratings'];
          ratings.forEach(rate => {
            total = total + rate.stars;
          });
          const averageStars = Math.trunc((total/ratings.length));
          return averageStars >= rate;
        });

        if (this.searchedRestaurant !== '') {
          this.filterGivenArray(restaurantsFromRateFilter, 'restaurantName', this.searchedRestaurant);
        } else { this.restaurantsDisplayed = restaurantsFromRateFilter; }

        this.changeDisplayedRate(rate);
        this.displayedRestaurantsVisible();
        this.checkMarkersVisibility();
      },

      /**
       * Function for the search bar. Update the array restaurantsDisplayed depends on the value of searchRestaurant
       */
      logSearchedRestaurant() {
        if (this.searchedRestaurant !== '') {
          if (this.filteredRate === 0) {
            this.filterGivenArray(this.restaurants, 'restaurantName', this.searchedRestaurant);
            this.displayedRestaurantsVisible();
            this.checkMarkersVisibility();
            return
          }
          if (this.filteredRate > 0) {
            this.setRateFilter(this.filteredRate);
            this.filterGivenArray(this.restaurantsDisplayed, 'restaurantName', this.searchedRestaurant);
            this.displayedRestaurantsVisible();
            this.checkMarkersVisibility();
            return
          }
        }

        if (this.filteredRate === 0) {
          this.restaurantsDisplayed = this.restaurants;
          this.displayedRestaurantsVisible();
          this.checkMarkersVisibility();
        } else { this.setRateFilter(this.filteredRate); }
      }
    }
  }
</script>

<style scoped lang="scss">
  .chips{
    border: 1px solid #fff;
    color: #fff !important;
    &:hover {
      color: #42b883 !important;
      .md-icon{
        color: #42b883 !important;
      }
    }
  }

  .md-list-item-text * {
    width: auto !important;
    margin-right: 15px !important;
  }
</style>
