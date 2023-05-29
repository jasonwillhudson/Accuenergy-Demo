<template>
    <main>
        <LMap ref="map" :zoom="zoom" :center="center" :useGlobalLeaflet="false" @update:center="updateCenter">
            <LTileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" layer-type="base"
                name="Stadia Maps Basemap" />

            <LMarker v-for="(loc) of searchedLocations" :latLng="[loc.latitude, loc.longitude]" />
        </LMap>
    </main>
</template>
  
<script>
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";
import 'leaflet/dist/leaflet.css';

export default {
    components: {
        LMap,
        LTileLayer,
        LMarker
    },
    props: ['location', 'searchedLocations'],
    data() {
        return {
            center: [49.1193089, 6.1757156],
            zoom: 3,
        }
    },
    watch: {
        location: function (newVal, oldVal) {
            if (newVal != null) {
                this.center = [newVal[0], newVal[1]];
                this.zoom = 10;
            }
        }
    },
    methods: {
        updateCenter(center) {
            this.center = [center.lat, center.lng];
        }
    },
}
</script>
  
<style>
main {
    height: 600px;
    width: 100%;
}
</style>