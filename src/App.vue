<template xmlns:>
  <div id="app">
    <div>cevher component</div>
    <hr />
    <div>
      <input v-model="columnName" type="text" name id placeholder="columnName" />
      <input v-model="columnValue" type="text" name id placeholder="columnValue" />
      <button v-on:click="filterClick">Filter</button>
    </div>
    <div style="width:500px;height:500px;margin:20px;box-shadow:0px 0px 5px 1px black;">
      <cevher :config="config" :filter="filterText"></cevher>
    </div>
  </div>
</template>

<script>
import cevher from "./components/cevher";
import {
  createProj,
  addProj,
  findPointOnSurface,
  createStyle,
  createMultiPointGeom,
  loadingBBox
} from "vuelayers/lib/ol-ext";

export default {
  components: {
    cevher
  },
  methods: {
    filterClick: function name() {
      this.filterText = {
        layerId: "5",
        columnName: this.columnName,
        columnValue: this.columnValue
      };
    }
  },
  data() {
    return {
      columnName: "fid",
      columnValue: "25910",
      filterText: "",
      config: {
        displayCevher: true,
        zoom: 10,
        center: [28.8724219692539, 41.04775534936306],
        // imagerySet = "Road" - "Aerial" - "AerialWithLabels" - "CanvasGray"
        baseLayers: [
          {
            name: "osm",
            title: "OpenStreetMap",
            visible: true
          },
          {
            name: "sputnik",
            title: "Sputnik Maps",
            visible: false
          },
          {
            name: "bingmaps",
            title: "Bing Maps",
            apiKey: "---",
            imagerySet: "CanvasGray",
            visible: false
          }
        ],
        layers: [
          {
            id: "1",
            title: "Test Layer",
            cmp: "vl-layer-image",
            visible: false,
            source: {
              cmp: "vl-source-image-wms",
              url: "http://0.0.0.0:8080/geoserver/wms",
              layers: "cite:test",
              serverType: "geoserver"
            }
          },
          {
            id: "2",
            title: "WFS - Test (Canada water areas)",
            cmp: "vl-layer-vector",
            visible: false,
            renderMode: "image",
            source: {
              cmp: "vl-source-vector",
              features: [],
              url(extent, resolution, projection) {
                return (
                  "https://ahocevar.com/geoserver/wfs?service=WFS&" +
                  "version=1.1.0&request=GetFeature&typename=osm:water_areas&" +
                  "outputFormat=application/json&srsname=" +
                  projection +
                  "&" +
                  "bbox=" +
                  extent.join(",") +
                  "," +
                  projection
                );
              },
              strategyFactory() {
                return loadingBBox;
              }
            }
          }
        ]
      }
    };
  }
};
</script>