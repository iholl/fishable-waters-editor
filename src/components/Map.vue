<template>
  <div class="mapdiv"></div>
</template>

<script>
import MapView from '@arcgis/core/views/MapView'
import Map from "@arcgis/core/Map"

import FeatureLayer from '@arcgis/core/layers/FeatureLayer'

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

    view.popup = {
      dockEnabled: true,
      dockOptions: {
        breakpoint: false,
        buttonEnabled: false,
        position: "bottom-right"
      }
    }

    var fishableWatersTemplate = {
      title: "{water_name}",
      content: [
        {
          type: "fields",
          fieldInfos: [
            {
              fieldName: "region",
              label: "Region"
            },
            {
              fieldName: "county",
              label: "County"
            },
            {
              fieldName: "water_type",
              label: "Water Type"
            }
          ],
        }
      ]
    }
    const fishableWatersLayer = new FeatureLayer({
      url: "https://services.arcgis.com/RyxlXSfFi87rAosq/arcgis/rest/services/esmeralda_fishable_nhd/FeatureServer/0",
      popupEnabled: true,
      popupTemplate: fishableWatersTemplate
    })
    map.add(fishableWatersLayer)
  }
}
</script>

<style>
@import 'https://js.arcgis.com/4.19/@arcgis/core/assets/esri/themes/dark/main.css';
.mapdiv {
  height: 100vh;
}

.esri-popup__main-container {
  border-radius: 4px;
}
</style>
