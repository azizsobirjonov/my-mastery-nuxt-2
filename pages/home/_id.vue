<template>
    <div>
        <div style="display: flex;">
            <img v-for="img in home.images" :key="img" :src="img" alt="desc" width="200" height="150">
        </div>
        {{ home.title }} <br>
        ${{ home.pricePerNight }} / night <br />
        <img src="/images/marker.svg" width="20" height="20">
        {{ home.location.address }} {{ home.location.city }} {{ home.location.state }} {{ home.location.country }} <br />
        <img src="/images/star.svg" width="20" height="20">
        {{ home.reviewValue }} <br />
        {{ home.guests }} guests, {{ home.bedrooms }} rooms, {{ home.beds }} beds, {{ home.bathrooms }} bath <br />

        <div style="height: 800px; width: 800px;" ref="map"></div>

    </div>
</template>

<script>
import homes from '~/data/homes'

export default {
    head() {
        return {
            title: this.home.title,
            script: [{
                src: "https://maps.googleapis.com/maps/api/js?key=AIzaSyCheiUc_ZTzR0wSAjQJkuYUUTg6eGS4VcM&libraries=places&callback=initMap",
                hid: "map",
                async: true,
                skip: process.client && window.mapLoaded
            },
            {
                innerHTML: "window.initMap = function(){ window.mapLoaded = true }",
                hid: 'map-init'
            }]
        }
    },
    data() {
        return {
            home: {}
        }
    },
    methods: {
        showMap() {
            const mapOptions = {
                zoom: 18,
                center: new window.google.maps.LatLng(this.home._geoloc.lat, this.home._geoloc.lng),
                disableDefaultUI: true,
                zoomControl: true
            }
            const map = new window.google.maps.Map(this.$refs.map, mapOptions)
            const position = new window.google.maps.LatLng(this.home._geoloc.lat, this.home._geoloc.lng)
            const marker = new window.google.maps.Marker({ position })
            marker.setMap(map)
        }
    },
    created() {
        const home = homes.find(e => e.objectID == this.$route.params.id)
        this.home = home
    },
    mounted() {
        const timer = setInterval(() => {
            if (window.initMap) {
                clearInterval(timer)
                this.showMap()
            }
        }, 200);
    }
}
</script>
