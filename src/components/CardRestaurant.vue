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
            <img :src="image" alt="Photo du restaurant">
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
        image: null
    }),
    created: function() {
        this.setRate()
    },
    watch: {
            restaurant() {
                this.setRate()
            }
    },
    mounted: function() {
        // this.image = 'https://maps.googleapis.com/maps/api/streetview?location='+this.restaurant.lat+','+this.restaurant.long+'&size=200x200&key='+process.env.VUE_APP_APIKEY
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

<style lang='scss'>
.test {
    color: red;
}
</style>