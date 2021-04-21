<template>
  <div class="calendar" @scroll="onScroll">
    <h3>Календарь снимков</h3>
    <div v-for="(date, index) in dates" :key="index">
      <div class="calendar__map" :ref="`mapContainer${index + 1}`" />
      <span class="calendar__btn" @click="handleClick(date, index)">
        {{ date.toLocaleDateString() }}
      </span>
    </div>
  </div>
</template>

<script>
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
        if (i > this.dates.length - 5) {
          const tilePath =
            "wmts/epsg3857/best/" +
            "MODIS_Terra_CorrectedReflectance_TrueColor/default/" +
            `${date
              .toLocaleDateString()
              .split(".")
              .reverse()
              .join("-")}/GoogleMapsCompatible_Level9/{z}/{y}/{x}.jpg`;

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
        }
      });
    },
    onScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (Math.ceil(scrollTop) + clientHeight >= scrollHeight) {
        this.getLastDates(this.dates[this.dates.length - 1]);
      }
    },
    getLastDates(date) {
      for (let i = 1; i < 5; i++) {
        let newDate = new Date(date.getTime());
        newDate.setDate(date.getDate() - i);
        this.dates.push(newDate);
      }
    },
    handleClick(date, index) {
      this.$emit("handleAddDay", date);
      Object.values(this.$refs).forEach((container) => {
        container[0].style.border = "";
      });
      this.$refs[`mapContainer${index + 1}`][0].style.border = "2px solid #00f";
    },
  },
  mounted() {
    const currentDate = new Date();
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

  &__btn {
    cursor: pointer;
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
