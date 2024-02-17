<template>
  <div class="style-map">
    <h2>Road Trip Sample</h2>
    <div v-if="location">
      <p><strong>Latitude</strong>: {{ location.lat }}</p>
      <p><strong>Longitude</strong>: {{ location.lng }}</p>
      <p><strong>Address</strong>: {{ address }}</p>
    </div>
    <l-map
      ref="map"
      style="height: 80vh; width: 80%"
      :zoom="zoom"
      :center="center"
      @leaflet-ready="onMapReady"
    >
      <l-marker :lat-lng="markerLatLng"></l-marker>
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <v-geosearch :options="geosearchOptions"></v-geosearch>
    </l-map>
  </div>
</template>

<script>
import "leaflet-geosearch/dist/geosearch.css";
import { OpenStreetMapProvider } from "leaflet-geosearch";
import VGeosearch from "vue2-leaflet-geosearch";
import "leaflet-routing-machine/dist/leaflet-routing-machine.css";
import "leaflet/dist/leaflet.css";
import leaflet from "leaflet";

export default {
  components: {
    "v-geosearch": VGeosearch,
  },
  name: "HelloWorld",
  data() {
    return {
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 14,
      center: [17.4436222, 78.3519638],
      markerLatLng: [17.4436222, 78.3519638],
      address: null,
      location: null,
      geosearchOptions: {
        style: "bar",
        provider: new OpenStreetMapProvider(),
        showMarker: true,
        autoClose: true,
        keepResult: true,
        searchLabel: "Enter address",
      },
      geosearchProvider: new OpenStreetMapProvider(),
      routeService: null,
    };
  },
  mounted() {
    this.$refs.map.mapObject.on("geosearch/showlocation", this.onSearch);
  },
  beforeDestroy() {
    return this.layer ? this.layer.remove() : null;
  },
  methods: {
    onSearch(value) {
      this.location = { lat: value.location.y, lng: value.location.x };
      this.address = value.location.label;
    },
    onMapReady(map) {
      leaflet.Routing.control({
        waypoints: [
          leaflet.latLng(this.startLatLng),
          leaflet.latLng(this.endLatLng),
        ],
        lineOptions: {
          styles: [{ color: "green", opacity: 1, weight: 5 }],
        },
      }).addTo(map);
    },
  },
};
</script>

<style scoped>
.style-map {
  height: 100vh;
}
.leaflet-control-geosearch {
  outline: none;
  border: none;
}
</style>
