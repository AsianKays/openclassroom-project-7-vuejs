<template>
    <div id="google-map"></div>
</template>

<script>
var GoogleMapsLoader = require('google-maps');
export default {
    name: 'GoogleMaps',
    mounted() {
        // GoogleMapsLoader.KEY = 'my-key';
        GoogleMapsLoader.VERSION = '3.39'
        GoogleMapsLoader.REGION = 'fr'
        
        GoogleMapsLoader.load((google) => {
            let map = new google.maps.Map(document.getElementById('google-map'), {
                zoom: 12,
                center: {lat:48.873781, lng: 2.822075}
            })
            this.createMarker(google, map, 'red')
        })
    },
    methods: {
        createMarker(google, map, color) {
            let url = "http://maps.google.com/mapfiles/ms/icons/"
            url += color + "-dot.png"

            new google.maps.Marker(
                {
                    position: {lat:48.873781, lng: 2.822075},
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