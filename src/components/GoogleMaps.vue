<template>
    <div id="google-map"></div>
</template>

<script>
var GoogleMapsLoader = require('google-maps');
export default {
    name: 'GoogleMaps',
    props: {
        restaurants: {
            type: Array,
            required: false
        }
    },
    async mounted() {
        // GoogleMapsLoader.KEY = 'my-key';
        GoogleMapsLoader.VERSION = '3.39'
        GoogleMapsLoader.REGION = 'fr'
        let userPosition = await this.getUserLocalisation()
        GoogleMapsLoader.load((google) => {
            let map = new google.maps.Map(document.getElementById('google-map'), {
                zoom: 12,
                center: userPosition
            })
            if(this.restaurants.length !== 0) {
                this.restaurants.forEach(restaurant => {
                    let position = {}
                    position.lat = restaurant.lat
                    position.lng = restaurant.long
                    this.createMarker(google, map, position, 'red')
                });
            }
            this.createMarker(google, map, userPosition, 'blue')
        })
    },
    methods: {
        getUserLocalisation() {
            return new Promise(
                (resolve) => {
                    if(navigator.geolocation) {
                        navigator.geolocation.getCurrentPosition(
                            position => {
                                let location = {
                                    lat: position.coords.latitude,
                                    lng: position.coords.longitude
                                }
                                resolve(location)
                            }
                        )
                    }
                }
            )
        },
        createMarker(google, map, position, color) {
            let url = "http://maps.google.com/mapfiles/ms/icons/"
            url += color + "-dot.png"

            new google.maps.Marker(
                {
                    position: position,
                    map: map,
                    icon: {
                        url: url
                    }
                }
            )
        }
    }
}
</script>

<style>
#google-map {
    height: 100%;
    width: 100%;
}
</style>