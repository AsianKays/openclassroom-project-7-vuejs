<template>
    <div class="md-layout">
        <div class="md-layout-item">
            <h1>Restaurants</h1>

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
                    <md-menu-item @click="test(0)" >
                        <div>
                            Toutes les notes
                        </div>
                    </md-menu-item>
                    <md-menu-item @click="setRateFilter(item.nbStars.length)" v-for="(item, index) in starsForFilterOptions" :key="index">
                        <div>
                            <icon-star v-for="(star, index) in item.nbStars" :key="index"></icon-star>
                        </div>
                    </md-menu-item>
                </md-menu-content>
            </md-menu>

            <div v-for="(restaurant, index) in restaurantsDisplayed" :key="index">
                <keep-alive>
                    <CardRestaurant v-bind:restaurant.sync=restaurant></CardRestaurant>
                </keep-alive>
            </div>
        </div>
    </div>
</template>

<script>
// https://medium.com/devmarketer/how-to-add-conditional-statements-to-v-for-loops-in-vue-js-c0b4d17e7dfd

import CardRestaurant from './CardRestaurant.vue'
import {eventBus} from "../main"
import IconStar from './icons/IconStar.vue'


export default {
    name: "ListRestaurants",
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
        // this.displayedRestaurantsVisible()
        eventBus.$on('markersVisible', (_markersVisible) => {
            let restaurantsVisible = []
            _markersVisible.forEach((index) => {
                restaurantsVisible.push(this.restaurants[index])
            })
            this.restaurantsDisplayed = restaurantsVisible
        })
    },
    methods: {
        logSearchedRestaurant() {
            if(this.searchedRestaurant !== '') {
                this.restaurantsDisplayed = this.restaurants.filter(restaurant => 
                    restaurant['restaurantName'].toLowerCase().includes(this.searchedRestaurant.toLowerCase())
                )
            }
            this.displayedRestaurantsVisible()
        },
        displayedRestaurantsVisible() {
            eventBus.$emit('restaurantsDisplayed', this.restaurantsDisplayed)
        },
        checkMarkers() {
            eventBus.$emit('checkMarkers')
        },
        changeDisplayedRate(rate) {
            switch(rate) {
                case 0:
                    this.displayedRate = 'Note'
                    break;
                case 2:
                    this.displayedRate = '2+'
                    break;
                case 3:
                    this.displayedRate = '3+'
                    break;
                case 4:
                    this.displayedRate = '4+'
                    break;
                case 5:
                    this.displayedRate = '5+'
                    break;
            }

        },
        setRateFilter(rate) {
            this.restaurantsDisplayed = this.restaurants.filter(restaurant => {
                let total = 0
                let ratings = restaurant['ratings']

                ratings.forEach(rate => {
                    total = total + rate.stars
                });

                let averageStars = Math.trunc((total/ratings.length))

                return averageStars >= rate
            })
            this.changeDisplayedRate(rate)
            this.displayedRestaurantsVisible()
            this.checkMarkers()
        }
    }
}
</script>

<style lang="scss">
@import "~vue-material/dist/theme/engine"; // Import the theme engine
@include md-register-theme("default", (
  primary: md-get-palette-color(green, A200), // The primary color of your application
  accent: md-get-palette-color(pink, 500), // The accent or secondary color
  theme: dark // This can be dark or light
));
@import "~vue-material/dist/theme/all"; // Apply the theme

.chips{
    border: 1px solid #fff;
    &:hover {
        .md-icon{
            color: #313131 !important;
        }
    }
}

.md-list-item-text * {
    width: auto !important;
    margin-right: 15px !important;
}
</style>