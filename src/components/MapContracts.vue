<template>
  <div id="app">
    <l-map ref="map_base" :zoom="zoom" :center="center" style="height: 90vh">
      <l-tile-layer :url="url" :attribution="attribution" :max-bounds="bounds" :bounds="bounds" />
      <l-geo-json ref="map_departamentos" :geojson="departamentosGeojson" />
      <l-choropleth-layer ref="map_distritos" :data="distritoResultados" titleKey="name" idKey="IDDIST" :value="value" geojsonIdKey="IDDIST" :geojson="distritosGeojson" :colorScale="colorScale">
        <template slot-scope="props">
          <l-info-control :item="currentItem=props.currentItem" :unit="props.unit" title="Distrito" placeholder="Seleccionar para conocer la composición de votos"/>
          <l-reference-chart title="Votos preferenciales emitidos" :colorScale="colorScale" :min="props.min" :max="props.max" position="topright"/>
        </template>
      </l-choropleth-layer>
    </l-map>
  </div>
</template>

<script>
import { InfoControl, ReferenceChart, ChoroplethLayer } from 'vue-choropleth'
import topojson from 'topojson'
import { LMap, LTileLayer, LGeoJson } from 'vue2-leaflet';
import { latLngBounds } from 'leaflet'
import merge from "lodash/merge"

export default {
  name: "MapContracts",
  components: { 
    LMap,
    LTileLayer,
    LGeoJson,
    'l-info-control': InfoControl, 
    'l-reference-chart': ReferenceChart, 
    'l-choropleth-layer': ChoroplethLayer 
  },
  props: {
    departamento:  {
      type: String,
      default: "peru_departamentos",
      required: false
    }
  },
  data() {
    return {
      bounds: [],
      currentItem: {},
      departamentosGeojson : {},
      resultados: this.distritoResultados,
      colorScale: ["e7d090", "e9ae7b", "de7062"],
      zoom: 5,
      center: [-9.132595, -75.331763],
      value: {
        key: "total",
        metric: " de votos preferenciales emitidos"
      },
      currentStrokeColor: '3d3213',
      url: "http://{s}.tile.osm.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
    }
  },
  created() {
    this.departamentosGeojson = require("../data/lima.json")
  },
  methods: {
    getBounds() {
      if(this.$refs["map_departamentos"]) {
        return this.$refs["map_departamentos"].getBounds()
      }

      return []
    }
  },
  computed: {
    distritosGeojson() {
      return require("../data/"+this.departamento+".json")
    },
    distritoResultados() {
      return require("../data/resultados_"+this.departamento+".json")
    },
  },
  watch: {
    departamento(value) {
      this.bounds = this.getBounds()
    }
  }
}
</script>
<style>
  @import "~leaflet/dist/leaflet.css";

  body {
    background-color: #e7d090;
    margin-left: 100px;
    margin-right: 100px;
  }

  #map {
    background-color: #eee;
  }
</style>