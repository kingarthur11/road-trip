<template>
  <div class="style-map">
    <div v-if="location">
      <p>location:{{ location }}</p>
      <p>address: {{ address }}</p>
    </div>
    <l-map style="height: 80vh; width: 80%" :zoom="zoom" :center="center">
      <l-marker :lat-lng="markerLatLng"></l-marker>
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <v-geosearch :options="geosearchOptions"></v-geosearch>
      <l-routing-machine :service="routeService"></l-routing-machine>
      <!-- <l-routing-machine :waypoints="waypoints" /> -->
    </l-map>
  </div>
</template>

<script>
import "leaflet-geosearch/dist/geosearch.css";
import { OpenStreetMapProvider } from "leaflet-geosearch";
import VGeosearch from "vue2-leaflet-geosearch";
import "leaflet-routing-machine/dist/leaflet-routing-machine.css";
import { LRoutingMachine } from "vue2-leaflet";
import "leaflet/dist/leaflet.css";
import leaflet from "leaflet";

export default {
  components: {
    "v-geosearch": VGeosearch,
    LRoutingMachine,
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
      startPoint: "",
      endPoint: "",
    };
  },
  mounted() {
    this.$refs.map.mapObject.on("geosearch/showlocation", this.onSearch);
    const routingControl = leaflet.Routing.control({
      waypoints: [leaflet.latLng(17.4436222, 78.3519638)],
      routeWhileDragging: true,
    });
    routingControl.addTo(this.$refs.map.mapObject);
    this.routeService = leaflet.Routing.control({
      waypoints: [
        leaflet.latLng(47.41322, -1.219482),
        leaflet.latLng(47.42842, -1.214982),
      ],
    }).addTo(this.$refs.map.mapObject);
  },
  beforeDestroy() {
    return this.layer ? this.layer.remove() : null;
  },
  methods: {
    onSearch(value) {
      this.location = { lat: value.location.y, lng: value.location.x };
      this.address = value.location.label;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.style-map {
  height: 100vh;
}
</style>
