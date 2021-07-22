<template>
  <div class="mapdiv"></div>
</template>

<script>
import MapView from '@arcgis/core/views/MapView'

import Map from '@arcgis/core/Map'

import FeatureLayer from '@arcgis/core/layers/FeatureLayer'

import Expand from '@arcgis/core/widgets/Expand'
import LayerList from '@arcgis/core/widgets/LayerList'
import BasemapGallery from '@arcgis/core/widgets/BasemapGallery'
import Editor from '@arcgis/core/widgets/Editor'
import Search from '@arcgis/core/widgets/Search'

export default {
  name: 'Map',
  async mounted () {
    let highlight

    const fishableWatersTemplate = {
      title: '{water_name}',
      content: [
        {
          type: 'fields',
          fieldInfos: [
            {
              fieldName: 'region',
              label: 'Region'
            },
            {
              fieldName: 'county',
              label: 'County'
            },
            {
              fieldName: 'water_type',
              label: 'Water Type'
            },
            {
              fieldName: 'species_1',
              label: 'Species 1'
            },
            {
              fieldName: 'species_2',
              label: 'Species 2'
            }
          ],
        }
      ]
    }
    const fishableWatersLayer = new FeatureLayer({
      url: 'https://services.arcgis.com/RyxlXSfFi87rAosq/arcgis/rest/services/esmeralda_fishable_nhd/FeatureServer/0',
      id: 'Fishable Waters',
      popupEnabled: true,
      popupTemplate: fishableWatersTemplate
    })

    const map = new Map ({
      basemap: 'topo-vector'
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
        position: 'bottom-right'
      }
    }

    view.when(async () => {
      // Search
      const searchWidget = new Search({
        view: view,
        allPlaceholder: 'Fishable Water Name',
        includeDefaultSources: false,
        locationEnabled: false,
        sources: [
          {
            layer: fishableWatersLayer,
            searchFields: ['water_name'],
            displayField: 'water_name',
            exactMatch: false
          }
        ]
      })
      view.ui.add(searchWidget, 'top-right')

      // Editor Fishable Waters fields
      const fishableWaterFields = {
        layer: fishableWatersLayer,
        fieldConfig: [{
          name: 'species_1',
          label: 'Species 1'
        },
        {
          name: 'species_2',
          label: 'Species 2'
        }]
      }

      // Editor
      const editor = new Editor({
        view: view,
        layerInfos: [{
          layer: fishableWatersLayer,
          fieldConfig: [fishableWaterFields]
        }],
        // snappingOptions: {
        //   enabled: true,
        //   selfEnabled: true,
        //   featureEnabled: true,
        //   featureSources: [{
        //     layer: nhdLayer
        //   }],
        //   distance: 20,
        // },
        container: document.createElement('div')
      })
      var editorExpand = new Expand({
        view: view,
        content: editor
      })
      view.ui.add(editorExpand, 'top-right')

      // Layer List
      const layerList = new LayerList({
        view: view,
        container: document.createElement('div'),
        listItemCreatedFunction: function (event) {
          var item = event.item
          if (item.title === 'Esmeralda fishable nhd'){
            item.title = 'Fishable Waters'
          }
        }
      })
      const layerListExpand = new Expand({
          view: view,
          content: layerList,
      })
      view.ui.add(layerListExpand, 'top-left')

      // Basemap Gallery 
      const basemapGallery = new BasemapGallery({
        view: view,
        container: document.createElement('div')
      })
      const basemapGalleryExpand = new Expand({
          view: view,
          content: basemapGallery
        })
      view.ui.add(basemapGalleryExpand, 'bottom-left')

      // Add Layers to map after view has loaded
      map.add(fishableWatersLayer)
    })

    // Function to unselect features
    function unselectFeature() {
      if (highlight) {
        highlight.remove();
      }
    }

    view.on('click', () => {
      // Unselect any currently selected features
      unselectFeature();
    })
  }
}
</script>

<style>
@import 'https://js.arcgis.com/4.19/@arcgis/core/assets/esri/themes/dark/main.css';

.mapdiv {
  height: 100vh;
}
</style>
