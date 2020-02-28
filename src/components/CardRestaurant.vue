<template>
  <div class="hover" @click="updateStateReviews(true)">
    <md-card class="md-primary" md-theme="black-card">
      <md-card-header>
        <md-card-header-text>
          <div class="md-title">
            {{ restaurant.restaurantName }}
          </div>
          <div class="md-subhead">
            {{ restaurant.address }}
          </div>
          <icon-star v-for="i in averageRate" :key="i"></icon-star>
          <icon-star v-if="isHalf" format="half"></icon-star>
        </md-card-header-text>

        <md-card-media>
          <img :src="image" alt="Photo du restaurant">
        </md-card-media>
      </md-card-header>
    </md-card>
    <ReviewsRestaurant :restaurantName="restaurant.restaurantName" :state="state" :reviews="restaurant.ratings" @closed="updateStateReviews"></ReviewsRestaurant>
  </div>
</template>

<script>
  import IconStar from './icons/IconStar.vue';
  import ReviewsRestaurant from './ReviewsRestaurant.vue';
  import { eventBus } from "../main";

  export default {
    name: 'CardRestaurant',
    components: {
      IconStar,
      ReviewsRestaurant
    },
    props: {
      restaurant: {
        type: Object,
        required: true
      }
    },
    data: () => ({
      averageRate: 0,
      averageRateDecimal: 0,
      isHalf: false,
      image: '',
      state: false
    }),
    watch: {
      restaurant: {
        immediate: true,
        handler() {
          this.setRate()
        }
      }
    },
    mounted: function() {
      eventBus.$on('newReview' + this.restaurant.restaurantName, (newReview) => {
        this.restaurant.ratings.push(newReview);
        this.setRate()
      })
      this.image = 'https://maps.googleapis.com/maps/api/streetview?location='+this.restaurant.lat+','+this.restaurant.long+'&size=200x200&key='+process.env.VUE_APP_APIKEY
    },
    methods: {
      /**
       * Set the average rate of the restaurant
       */
      setRate() {
        let total = 0;
        const ratings = this.restaurant.ratings;

        if (ratings.length > 0) {
          ratings.forEach(rate => {
            total = total + rate.stars;
          });

          this.averageRate = Math.trunc((total/ratings.length));
          const decimal = (total/ratings.length)%1;

          if (decimal !== 0) {
            this.isHalf = true;
            this.averageRateDecimal = decimal * 10;
          }
        }
      },

      /**
       * Update the state for the child component (showDialog)
       * @param {Boolean} state - Will define the state of the matched dialog component
       */
      updateStateReviews(state) {
        this.state = state
      }
    }
  }
</script>

<style scoped lang='scss'>
  .hover:hover {
    cursor: pointer;
    background-color: #42b883;
  }
</style>
