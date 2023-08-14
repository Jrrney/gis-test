<template>
  <div id="app">
    <div class="cell cell-map">
      <MapContainer
        :geojson="geojson"
        v-on:select="selected = $event"
      ></MapContainer>
    </div>
    <div class="cell cell-edit">
      <Edit :geojson="geojson" v-on:change="geojson = $event"></Edit>
    </div>
    <div class="cell cell-inspect">
      <Inspect :feature="selected"></Inspect>
    </div>
  </div>
</template>

<script>
import MapContainer from "./components/MapContainer";
import Edit from "./components/Edit";
import Inspect from "./components/Inspect";
import axios from "axios";

export default {
  name: "App",
  components: {
    Inspect,
    Edit,
    MapContainer,
  },
  data: () => ({
    selected: undefined,
    geojson: null,
  }),
  mounted: function () {
    this.pollGis();
  },
  methods: {
    pollGis: function () {
      axios
        .get("http://localhost:8112/services/gisbackend/v1_0/overlay")
        .then((response) => {
          this.geojson = response.data[0];
        })
        .catch((error) => {
          console.log(error);
        });

      // poll every 5 seconds
      setTimeout(this.pollGis, 5000);
    },
  },
};
</script>

<style>
html,
body {
  height: 100%;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  height: 100%;
  display: grid;
  grid-template-columns: 100vh;
  grid-auto-rows: 1fr;
  grid-gap: 1rem;
  padding: 1rem;
  box-sizing: border-box;
}

.cell {
  border-radius: 4px;
  background-color: lightgrey;
}

.cell-map {
  grid-column: 1;
  grid-row-start: 1;
  grid-row-end: 3;
}

.cell-edit {
  grid-column: 2;
  grid-row: 1;
}

.cell-inspect {
  grid-column: 2;
  grid-row: 2;
}
</style>
