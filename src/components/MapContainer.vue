<template>
  <div ref="map-root" style="width: 100%; height: 100%"></div>
</template>

<script>
import View from "ol/View";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
import GeoJSON from "ol/format/GeoJSON";

import "ol/ol.css";
import Style from "ol/style/Style";
import Fill from "ol/style/Fill";
import Text from "ol/style/Text";
import Stroke from "ol/style/Stroke";
import axios from "axios";
// import { fromLonLat } from "ol/proj";
// import ol from "ol";

export default {
  name: "MapContainer",
  components: {},
  props: {
    geojson: Object,
  },
  data: () => ({
    olMap: null,
    vectorLayer: null,
    selectedFeature: null,
    centered: false,
  }),
  mounted() {
    this.vectorLayer = new VectorLayer({
      source: new VectorSource({
        features: [],
      }),
    });

    this.olMap = new Map({
      target: this.$refs["map-root"],
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        this.vectorLayer,
      ],
      view: new View({
        zoom: 0,
        center: [0, 0],
        constrainResolution: true,
      }),
    });

    this.olMap.on("click", (event) => {
      const clicked = this.olMap.forEachFeatureAtPixel(
        event.pixel,
        (feature) => feature
      );
      if (clicked !== null) {
        const call = clicked.get("onClick");
        if (call) {
          axios
            .post(call.url, call.body, {
              headers: {
                "Content-Type": call["Content-Type"],
              },
            })
            .then((response) => {
              console.log(response);
            })
            .catch((error) => {
              console.log(error);
            });
        }
      }
    });

    this.olMap.on("pointermove", (event) => {
      const hovered = this.olMap.forEachFeatureAtPixel(
        event.pixel,
        (feature) => feature
      );
      if (hovered !== this.selectedFeature) {
        if (hovered && hovered.get("onClick")) {
          const style = hovered.getStyle();
          if (style) {
            style.setStroke(
              new Stroke({
                color: "blue",
                width: 10,
              })
            );
            this.vectorLayer.getSource().dispatchEvent("change");
          }
        }
        if (this.selectedFeature) {
          const style = this.selectedFeature.getStyle();
          if (style) {
            style.setStroke();
            this.vectorLayer.getSource().dispatchEvent("change");
          }
        }
        this.$set(this, "selectedFeature", hovered);
      }
    });

    this.updateSource(this.geojson);
  },
  watch: {
    geojson(value) {
      this.updateSource(value);
    },
    selectedFeature(value) {
      this.$emit("select", value);
    },
  },
  methods: {
    updateSource(geojson) {
      const source = this.vectorLayer.getSource();

      const features = new GeoJSON({
        featureProjection: "EPSG:3857",
      }).readFeatures(geojson);

      features.forEach((feature) => {
        const name = feature.get("name");
        if (name) {
          feature.setStyle(
            new Style({
              text: new Text({
                font: "30px sans-serif",
                text: name,
                fill: new Fill({
                  color: "black",
                }),
              }),
            })
          );
        }

        const fill = feature.get("fill");
        if (fill) {
          feature.setStyle(
            new Style({
              fill: new Fill({
                color: fill,
              }),
            })
          );
        }
      });

      source.clear();
      source.addFeatures(features);

      if (!this.centered) {
        this.centered = true;
        const view = this.olMap.getView();
        view.fit(source.getExtent());
      }
    },
  },
};
</script>
