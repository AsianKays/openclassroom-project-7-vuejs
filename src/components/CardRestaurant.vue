<template>
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
</template>

<script>
  import IconStar from './icons/IconStar.vue';

    export default {
      name: 'CardRestaurant',
      components: {
        IconStar
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
        image: ''
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
        // this.image = 'https://maps.googleapis.com/maps/api/streetview?location='+this.restaurant.lat+','+this.restaurant.long+'&size=200x200&key='+process.env.VUE_APP_APIKEY
      },
      methods: {
        /**
         * Set the average rate of the restaurant
         */
        setRate() {
          let total = 0;
          const ratings = this.restaurant['ratings'];

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
      }
    }
</script>

<style lang='scss'>

</style>
