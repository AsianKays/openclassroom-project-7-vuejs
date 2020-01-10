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
            <icon-star v-for="i in averageStars" :key="i"></icon-star>
            <icon-star v-if="isHalf" format="half"></icon-star>
        </md-card-header-text>

        <md-card-media>
            <img src="https://vuematerial.io/assets/examples/avatar-2.jpg" alt="Avatar">
        </md-card-media>
    </md-card-header>
    </md-card>
</template>

<script>
import IconStar from './icons/IconStar.vue'

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
        averageStars: 0,
        isHalf: false
    }),
    mounted: function() {
        let total = 0
        let ratings = this.restaurant['ratings']
        ratings.forEach(rate => {
            total = total + rate.stars
        });
        this.averageStars = Math.trunc((total/ratings.length))
        if((total/ratings.length)%1 !== 0) {
            this.isHalf = true
        }
    }
}
</script>

<style>
</style>