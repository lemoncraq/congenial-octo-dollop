<template>
  <div ref="map-root" style="width: 100%; height: 100%"></div>
</template>

<script>
import View from "ol/View";
import Map from "ol/Map";
import TileLayer from "ol/layer/Tile"
import OSM from "ol/source/OSM"
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
import GeoJSON from "ol/format/GeoJSON";

import 'ol/ol.css'

export default {
  name: "MapContainer",
  components: {},
  props: {
    geojson: {},
  },
  data() {
    return {
      olMap: null,
      vectorLayer: null
    }
  },
  mounted() {
    this.vectorLayer = new VectorLayer({
      source: new VectorSource({
        features: [], // the vector layer is now created empty
      }),
    })
    this.olMap = new Map({
      target: this.$refs['map-root'],
      layers: [
        new TileLayer({
          source: new OSM()
        }),
        this.vectorLayer
      ],
      view: new View({
        zoom: 0,
        center: [0, 0],
        constrainResolution: true,
      })
    })
    this.updateSource(this.geojson)
  },
  watch: {
    geojson(value) {
      // call `updateSource` whenever the input changes as well
      this.updateSource(value)
    }
  },
  methods: {
    // this will parse the input data and add it to the map
    updateSource(geojson) {
      const view = this.olMap.getView()
      const source = this.vectorLayer.getSource()

      const features = new GeoJSON({
        featureProjection: 'EPSG:3857',
      }).readFeatures(geojson)

      source.clear();
      source.addFeatures(features);

      // this zooms the view on the created object
      view.fit(source.getExtent())
    }
  }
}
</script>

<style scoped>

</style>