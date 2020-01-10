<template>
    <div id="google-map"></div>
</template>

<script>
// https://stackoverflow.com/questions/6219383/google-maps-api-3-check-if-marker-is-in-view

let GoogleMapsLoader = require('google-maps')
import {eventBus} from "../main"

export default {
    name: 'GoogleMaps',
    props: {
        restaurants: {
            type: Array,
            required: true
        }
    },
    data: () => ({
        restaurantsDisplayed: [],
        google: Object,
        map: Object,
        arrayMarkers: []
    }),
    created: async function() {
        await this.initGoogleMap()

        eventBus.$on('restaurantsDisplayed', (_restaurants) => {
            this.hideAllMarkers()
            this.restaurants.forEach((restaurant, index) => {
                _restaurants.forEach(_restaurant => {
                    if(restaurant.restaurantName === _restaurant.restaurantName) {
                        this.setMapOnIndex(true, index)
                    }
                });
            });
        })
    },
    mounted() {
        this.restaurantsDisplayed = this.restaurants
    },
    methods: {
        async initGoogleMap() {
            // GoogleMapsLoader.KEY = 'my-key';
            GoogleMapsLoader.VERSION = '3.39'
            GoogleMapsLoader.REGION = 'fr'
            let userPosition = await this.getUserLocalisation()
            GoogleMapsLoader.load((google) => {
                this.google = google
                this.map = new google.maps.Map(document.getElementById('google-map'), {
                    zoom: 12,
                    center: userPosition
                })
                this.createAllRestaurantsMarkers()
                this.createMarker(userPosition, 'blue')

                this.listenerBounds(google)
            })
        },
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
        createMarker(position, color) {
            let url = "http://maps.google.com/mapfiles/ms/icons/"
            url += color + "-dot.png"

            let marker = new this.google.maps.Marker(
                {
                    position: position,
                    map: this.map,
                    icon: {
                        url: url
                    }
                }
            )
            if(color === 'red') {
                this.arrayMarkers.push(marker)
            }
        },
        createAllRestaurantsMarkers() {
            if(this.restaurants.length !== 0) {
                this.restaurants.forEach(restaurant => {
                    let position = {}
                    position.lat = restaurant.lat
                    position.lng = restaurant.long
                    this.createMarker(position, 'red')
                })
            }
        },
        setMapOnAll(map) {
            this.arrayMarkers.forEach(marker => {
                marker.setVisible(map)
            })
        },
        setMapOnIndex(map, index) {
            this.arrayMarkers[index].setVisible(map)
        },
        hideAllMarkers() {
            this.setMapOnAll(false)
        },
        listenerBounds(google) {
            google.maps.event.addListener(this.map, 'bounds_changed', () => {
				let markersVisible = []
				this.arrayMarkers.forEach((marker, index) => {
					if (this.map.getBounds().contains(marker.getPosition()) && marker.visible) {
						markersVisible.push(index)
					}
				})
				eventBus.$emit('markersVisible', markersVisible)
			});
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