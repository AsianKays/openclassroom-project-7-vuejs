<template>
    <div class="md-layout">
        <div class="md-layout-item">
            <h1>Restaurants</h1>

            <md-field>
                <label>Rechercher un restaurant</label>
                <md-input v-model="searchedRestaurant" @input=logSearchedRestaurant></md-input>
            </md-field>

            <div v-for="(restaurant, index) in restaurantsDisplayed" :key="index">
                <CardRestaurant :restaurant=restaurant></CardRestaurant>
            </div>
        </div>
    </div>
</template>

<script>
// https://medium.com/devmarketer/how-to-add-conditional-statements-to-v-for-loops-in-vue-js-c0b4d17e7dfd

import jsonRestaurants from '../json/restaurants.json'
import CardRestaurant from './CardRestaurant.vue'
export default {
    name: "ListRestaurants",
    components: {
        CardRestaurant
    },
    data: () => ({
        searchedRestaurant: '',
        restaurants: jsonRestaurants,
        restaurantsDisplayed: jsonRestaurants
    }),
    mounted() {
        /* eslint-disable no-console */
        // console.log(this.restaurants)
        /* eslint-enable no-console */
    },
    methods: {
        logSearchedRestaurant() {
            this.restaurantsDisplayed = this.restaurants
            if(this.searchedRestaurant !== '') {
                this.restaurantsDisplayed = this.restaurants.filter(restaurant => 
                    restaurant['restaurantName'].toLowerCase().includes(this.searchedRestaurant.toLowerCase())
                )
                /* eslint-disable no-console */
                // console.log('')
                /* eslint-enable no-console */
            }
        }
    }
}
</script>

<style lang="css">
</style>