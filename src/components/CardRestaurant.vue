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
        </md-card-header-text>

        <md-card-media>
            <img src="https://vuematerial.io/assets/examples/avatar-2.jpg" alt="Avatar">
        </md-card-media>
    </md-card-header>
    </md-card>
</template>

<script>
export default {
    name: 'CardRestaurant',
    props: {
        restaurant: {
            type: Object,
            required: true
        }
    }
}
</script>

<style>
</style>