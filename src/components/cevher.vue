<template>
  <div>
    <div style="position:relative;" v-if="config.displayCevher">
      <!-- map component -->
      <vl-map
        :load-tiles-while-animating="true"
        :load-tiles-while-interacting="true"
        data-projection="EPSG:4326"
        style="height: 500px"
        ref="map"
      >
        <!-- view component -->
        <vl-view
          ref="view"
          name="view"
          :zoom.sync="config.zoom"
          :min-zoom="2"
          :max-zoom="20"
          :center.sync="config.center"
          :rotation.sync="rotation"
        ></vl-view>
        <!--// view component -->

        <!-- interactions -->
        <vl-interaction-select :features.sync="selectedFeatures">
          <template slot-scope="select">
            <!-- select styles -->
            <vl-style-box>
              <vl-style-stroke color="#423e9e" :width="7"></vl-style-stroke>
              <vl-style-fill :color="[254, 178, 76, 0.7]"></vl-style-fill>
              <vl-style-circle :radius="5">
                <vl-style-stroke color="#423e9e" :width="7"></vl-style-stroke>
                <vl-style-fill :color="[254, 178, 76, 0.7]"></vl-style-fill>
              </vl-style-circle>
            </vl-style-box>
            <vl-style-box :z-index="1">
              <vl-style-stroke color="#d43f45" :width="2"></vl-style-stroke>
              <vl-style-circle :radius="5">
                <vl-style-stroke color="#d43f45" :width="2"></vl-style-stroke>
              </vl-style-circle>
            </vl-style-box>
            <!--// select styles -->
          </template>
        </vl-interaction-select>
        <!--// interactions -->

        <!-- base layers component -->
        <vl-layer-tile
          v-for="layer in config.baseLayers"
          :key="layer.name"
          :id="layer.name"
          :visible="layer.visible"
        >
          <component :is="'vl-source-' + layer.name" v-bind="layer" :imagery-set="layer.imagerySet"></component>
        </vl-layer-tile>
        <!--// base layers component -->

        <!-- layers component -->
        <component
          v-for="layer in config.layers"
          :is="layer.cmp"
          v-if="layer.visible"
          :key="layer.id"
          v-bind="layer"
        >
          <!-- source component -->
          <component :is="layer.source.cmp" v-bind="layer.source">
            <component
              v-if="layer.source.source"
              :is="layer.source.source.cmp"
              v-bind="layer.source.source"
            ></component>
          </component>
          <!--// source component -->
        </component>
        <!--// layers component -->
      </vl-map>
      <!--// map component -->

      <!-- layers panel -->
      <div style="position:absolute;top:0px;right:0px;width:200px;">
        <b-collapse class="panel box is-paddingless">
          <div slot="trigger" class="panel-heading">
            <div class="columns">
              <div class="column is-11" style="font-size:16px;">
                <strong>Layers</strong>
              </div>
            </div>
          </div>

          <div style="max-height:200px;overflow:auto;">
            <div style="font-size:13px;margin:5px;" v-for="layer in config.layers" :key="layer.id">
              <b-switch :key="layer.id" v-model="layer.visible">{{ layer.title }}</b-switch>
            </div>
          </div>
        </b-collapse>
      </div>
      <!--// layers panel -->

      <!-- select feature panel -->
      <div v-if="selectedPopup">
        <div
          style="position:absolute;bottom:0px;left:0px;width:200px;background-color:#f5f5f5;color:black;margin:5px;max-width:200px;max-height:200px;overflow:auto;"
        >
          <table border="1" style="width:100%">
            <tr>
              <th style="padding:5px;">Key</th>
              <th style="padding:5px;">Value</th>
            </tr>
            <tr v-for="item in selectedList" :key="item">
              <td style="padding:5px;">{{item[0]}}</td>
              <td style="padding:5px;">{{item[1]}}</td>
            </tr>
          </table>&nbsp;
        </div>
      </div>
      <!--// select feature panel -->
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import VueLayers from "vuelayers";
import "vuelayers/lib/style.css";
//import proj4 from "proj4";

Vue.use(VueLayers, {
  dataProjection: "EPSG:4326"
});

export default {
  name: "cevher",
  components: {
    Vue,
    VueLayers
  },
  props: {
    config: Object,
    filter: Object
  },
  data() {
    return {
      rotation: 0,
      selectedFeatures: [],
      selectedPopup: false,
      selectedList: []
    };
  },
  watch: {
    selectedFeatures: function(value) {
      console.clear();
      let selectObject = JSON.parse(JSON.stringify(value));

      if (selectObject.length > 0) {
        let list = Object.entries(selectObject[0].properties);
        this.selectedList = list;

        this.selectedPopup = true;
      } else {
        this.selectedPopup = false;
      }
    },
    filter: function(value) {
      if (value) {
        let layerId = value.layerId;
        let columnName = value.columnName;
        let columnValue = value.columnValue;

        if (layerId && columnName && columnValue) {
          console.clear();
          //console.log(this.config .layers.filter(x => x.id == layerId)[0].source.url);
          console.log(this.$refs);
          //this.config.layers.filter(x => x.id == layerId)[0].filter = "";
        }
      }
    }
  }
};
</script>

<style lang="css">
.test {
  background-color: antiquewhite;
}
</style>