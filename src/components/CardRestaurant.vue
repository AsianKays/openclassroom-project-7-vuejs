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
            {{averageStars}}.{{averageStarsDecimal}}
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
        averageStarsDecimal: 0,
        isHalf: false,
    }),
    created: function() {
        this.setRate()
    },
    watch: {
            restaurant() {
                this.setRate()
            }
    },
    methods: {
        setRate() {
            let total = 0
            let ratings = this.restaurant['ratings']

            ratings.forEach(rate => {
                total = total + rate.stars
            });

            let averageStars = Math.trunc((total/ratings.length))
            let decimal = (total/ratings.length)%1

            this.averageStars = averageStars
            if(decimal !== 0) {
                this.isHalf = true
                this.averageStarsDecimal = decimal * 10
            }
        }
    }
}
</script>

<style>
</style>