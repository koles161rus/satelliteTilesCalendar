<template>
  <div class="calendar">
    <h3>Календарь снимков</h3>
    <div class="calendar__map" ref="mapContainer1" />
    <button @click="$emit('handleAddDay', '2021-04-16')">16.04.2021</button>
    <div class="calendar__map" ref="mapContainer2" />
    <button @click="$emit('handleAddDay', '2021-04-15')">15.04.2021</button>
    <div class="calendar__map" ref="mapContainer3" />
    <button @click="$emit('handleAddDay', '2021-04-14')">14.04.2021</button>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  name: "Map",
  props: {
    date: String,
  },
  watch: {
    date() {
      this.mapInit();
    },
  },
  methods: {
    mapInit() {
      const tilePath1 =
        "wmts/epsg3857/best/" +
        "MODIS_Terra_CorrectedReflectance_TrueColor/default/" +
        "2021-04-16/GoogleMapsCompatible_Level9/{z}/{y}/{x}.jpg";

      const tilePath2 =
        "wmts/epsg3857/best/" +
        "MODIS_Terra_CorrectedReflectance_TrueColor/default/" +
        "2021-04-15/GoogleMapsCompatible_Level9/{z}/{y}/{x}.jpg";

      const tilePath3 =
        "wmts/epsg3857/best/" +
        "MODIS_Terra_CorrectedReflectance_TrueColor/default/" +
        "2021-04-14/GoogleMapsCompatible_Level9/{z}/{y}/{x}.jpg";

      // Add token here when using Mapbox layers
      mapboxgl.accessToken =
        "pk.eyJ1IjoiYnVyb3Z5YSIsImEiOiJjanVucnE3bHMweHRlM3pvNXAycXllaHl5In0.ytKUDnITJq8JScaXHW3qzQ";

      new mapboxgl.Map({
        container: this.$refs.mapContainer1,
        style: {
          version: 8,
          sources: {
            gibs: {
              type: "raster",
              tiles: [
                "https://gibs-a.earthdata.nasa.gov/" + tilePath1,
                "https://gibs-b.earthdata.nasa.gov/" + tilePath1,
                "https://gibs-c.earthdata.nasa.gov/" + tilePath1,
              ],
              tileSize: 256,
            },
          },
          layers: [
            {
              id: "gibs",
              type: "raster",
              source: "gibs",
              minzoom: 0,
              maxzoom: 0,
            },
          ],
        },
        center: [0, 0],
        minZoom: 0,
        maxZoom: 0,
        zoom: 0,
      });

      new mapboxgl.Map({
        container: this.$refs.mapContainer2,
        style: {
          version: 8,
          sources: {
            gibs: {
              type: "raster",
              tiles: [
                "https://gibs-a.earthdata.nasa.gov/" + tilePath2,
                "https://gibs-b.earthdata.nasa.gov/" + tilePath2,
                "https://gibs-c.earthdata.nasa.gov/" + tilePath2,
              ],
              tileSize: 256,
            },
          },
          layers: [
            {
              id: "gibs",
              type: "raster",
              source: "gibs",
              minzoom: 0,
              maxzoom: 0,
            },
          ],
        },
        center: [0, 0],
        minZoom: 0,
        maxZoom: 0,
        zoom: 0,
      });

      new mapboxgl.Map({
        container: this.$refs.mapContainer3,
        style: {
          version: 8,
          sources: {
            gibs: {
              type: "raster",
              tiles: [
                "https://gibs-a.earthdata.nasa.gov/" + tilePath3,
                "https://gibs-b.earthdata.nasa.gov/" + tilePath3,
                "https://gibs-c.earthdata.nasa.gov/" + tilePath3,
              ],
              tileSize: 256,
            },
          },
          layers: [
            {
              id: "gibs",
              type: "raster",
              source: "gibs",
              minzoom: 0,
              maxzoom: 0,
            },
          ],
        },
        center: [0, 0],
        minZoom: 0,
        maxZoom: 0,
        zoom: 0,
      });
    },
  },
  mounted() {
    this.mapInit();
  },
};
</script>

<style scoped lang="scss">
.calendar {
  width: 20%;

  &__map {
    width: 300px;
    height: 150px;
  }
}
</style>
