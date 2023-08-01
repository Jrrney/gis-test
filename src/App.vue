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

export default {
  name: "App",
  components: {
    Inspect,
    Edit,
    MapContainer,
  },
  data: () => ({
    selected: undefined,
    geojson: {
      features: [
        {
          geometry: {
            coordinates: [
              [
                [13.374787002, 52.480111062],
                [13.374787002, 52.479392288],
                [13.375988632, 52.479392288],
                [13.375988632, 52.480111062],
                [13.374787002, 52.480111062],
              ],
            ],
            type: "Polygon",
          },
          properties: {
            fill: "rgba(255, 0, 255, 0.5)",
          },
          type: "Feature",
          id: "sm62140ef8",
        },
        {
          geometry: {
            coordinates: [
              [
                [17.374787002, 53.480111062],
                [17.374787002, 53.479392288],
                [17.375988632, 53.479392288],
                [17.375988632, 53.480111062],
                [17.374787002, 53.480111062],
              ],
            ],
            type: "Polygon",
          },
          properties: {
            fill: "rgba(255, 0, 255, 0.5)",
          },
          type: "Feature",
          id: "sm6",
        },
        {
          geometry: {
            coordinates: [13.377249266, 52.479715735],
            type: "Point",
          },
          properties: { name: "Hello" },
          type: "Feature",
          id: "sm9982aad2",
        },
        {
          geometry: {
            coordinates: [17.377249266, 53.479715735],
            type: "Point",
          },
          properties: { name: "Hello from the other side" },
          type: "Feature",
          id: "sm9",
        },
      ],
      type: "FeatureCollection",
      id: "root",
    },
  }),
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
