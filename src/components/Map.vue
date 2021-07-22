<template>
  <div class="mapdiv"></div>
</template>

<script>
import MapView from '@arcgis/core/views/MapView'
import Map from "@arcgis/core/Map"

export default {
  name: 'Map',
  async mounted () {
    const map = new Map ({
      basemap: "topo-vector"
    })
    
    const view = new MapView ({
      container: this.$el,
      map: map,
      zoom: 6,
      center: [-117, 38.5]
    })

    view.constraints = {
      maxZoom: 17
    }
  }
}
</script>

<style>
@import 'https://js.arcgis.com/4.19/@arcgis/core/assets/esri/themes/dark/main.css';
.mapdiv {
  height: 100vh;
}
</style>
