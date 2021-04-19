<template>
  <div class="calendar" @scroll="onScroll">
    <h3>Календарь снимков</h3>
    <div v-for="(date, index) in dates" :key="index">
      <div class="calendar__map" :ref="`mapContainer${index + 1}`" />
      <button @click="$emit('handleAddDay', date)">
        {{ date.split("-").reverse().join(".") }}
      </button>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-debugger */
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";

export default {
  name: "Calendar",
  data() {
    return {
      dates: [],
    };
  },
  updated() {
    this.mapInit();
  },
  methods: {
    mapInit() {
      mapboxgl.accessToken =
        "pk.eyJ1IjoiYnVyb3Z5YSIsImEiOiJjanVucnE3bHMweHRlM3pvNXAycXllaHl5In0.ytKUDnITJq8JScaXHW3qzQ";

      this.dates.forEach((date, i) => {
        const tilePath =
          "wmts/epsg3857/best/" +
          "MODIS_Terra_CorrectedReflectance_TrueColor/default/" +
          `${date}/GoogleMapsCompatible_Level9/{z}/{y}/{x}.jpg`;

        new mapboxgl.Map({
          container: this.$refs[`mapContainer${i + 1}`][0],
          style: {
            version: 8,
            sources: {
              gibs: {
                type: "raster",
                tiles: [
                  "https://gibs-a.earthdata.nasa.gov/" + tilePath,
                  "https://gibs-b.earthdata.nasa.gov/" + tilePath,
                  "https://gibs-c.earthdata.nasa.gov/" + tilePath,
                ],
                tileSize: 256,
              },
            },
            layers: [
              {
                id: "gibs",
                type: "raster",
                source: "gibs",
              },
            ],
          },
          zoom: 0,
          dragPan: false,
          scrollZoom: false,
        });
      });
    },
    onScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (Math.ceil(scrollTop) + clientHeight >= scrollHeight) {
        this.getLastDates();
      }
    },
    getLastDates(date) {
      const dateArr = this.dates.length
        ? this.dates[this.dates.length - 1].split("-")
        : date.split("-");
      for (let i = 1; i < 5; i++) {
        const newDateArr = [...dateArr];
        newDateArr[newDateArr.length - 1] -= i;
        if (newDateArr[newDateArr.length - 1] < 1) {
          return;
        }
        if (newDateArr[newDateArr.length - 1] < 10) {
          newDateArr[newDateArr.length - 1] = `0${
            newDateArr[newDateArr.length - 1]
          }`;
        }
        this.dates.push(newDateArr.join("-"));
      }
    },
  },
  mounted() {
    const currentDate = new Date()
      .toLocaleDateString()
      .split(".")
      .reverse()
      .join("-");
    this.$emit("handleAddDay", currentDate);
    this.getLastDates(currentDate);
  },
};
</script>

<style scoped lang="scss">
.calendar {
  width: 20%;
  height: 100vh;
  background-color: #000;
  color: #fff;
  overflow-y: auto;
  overflow-x: hidden;
  scrollbar-width: thin;

  &__map {
    width: 280px;
    height: 200px;
    margin: 10px;
  }

  &::-webkit-scrollbar {
    width: 8px;
  }

  &::-webkit-scrollbar-track {
    background: #000;
  }

  &::-webkit-scrollbar-thumb {
    background: #787878;
    border: 1px solid #000;
  }
}
</style>
