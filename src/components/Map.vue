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

// import IdentityManager from '@arcgis/core/identity/IdentityManager'

export default {
  name: 'Map',
  async mounted () {
    let highlight

    // // NHD LAYER
    // const nhdLayer = new FeatureLayer({
    //   url: "https://hydro.nationalmap.gov/arcgis/rest/services/NHDPlus_HR/MapServer/2",
    //   opacity: 0.50
    // })

    // CURRENT TERRIBLE FISHABLE WATERS STREAM DATASET
    // const currentFishableWatersLayer = new FeatureLayer({
    //   url: "https://services.arcgis.com/RyxlXSfFi87rAosq/arcgis/rest/services/ndow_fishable_waters_gdb/FeatureServer/1"
    // })

    // FISHABLE WATERS LAYER
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
            },
            {
              fieldName: 'species_3',
              label: 'Species 3'
            },
            {
              fieldName: 'species_4',
              label: 'Species 4'
            },
            {
              fieldName: 'species_5',
              label: 'Species 5'
            },
            {
              fieldName: 'species_6',
              label: 'Species 6'
            },
            {
              fieldName: 'species_7',
              label: 'Species 7'
            },
            {
              fieldName: 'species_8',
              label: 'Species 8'
            },
            {
              fieldName: 'species_9',
              label: 'Species 9'
            },
            {
              fieldName: 'species_10',
              label: 'Species 10'
            },
            {
              fieldName: 'species_11',
              label: 'Species 11'
            },
          ],
        }
      ]
    }
    const fishableWatersLayer = new FeatureLayer({
      url: 'https://services.arcgis.com/RyxlXSfFi87rAosq/arcgis/rest/services/fishable_creeks_streams_rivers/FeatureServer/0',
      id: 'Fishable Creeks and Streams',
      popupEnabled: true,
      popupTemplate: fishableWatersTemplate,
      displayField: "water_name"
    })

    // MAP
    const map = new Map ({
      basemap: 'streets-night-vector'
    })
    
    // MAP VIEW
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

    // When view is loaded
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
          name: 'geo_update',
          label: 'Update Geometry (yes/no)'
        },
        {
          name: 'species_1',
          label: 'Species 1'
        },
        {
          name: 'species_2',
          label: 'Species 2'
        },
        {
          name: 'species_3',
          label: 'Species 3'
        },
        {
          name: 'species_4',
          label: 'Species 4'
        },
        {
          name: 'species_5',
          label: 'Species 5'
        },
        {
          name: 'species_6',
          label: 'Species 6'
        },
        {
          name: 'species_7',
          label: 'Species 7'
        },
        {
          name: 'species_8',
          label: 'Species 8'
        },
        {
          name: 'species_9',
          label: 'Species 9'
        },
        {
          name: 'species_10',
          label: 'Species 10'
        },
        {
          name: 'species_11',
          label: 'Species 11'
        },]
      }

      // Editor
      const editor = new Editor({
        view: view,
        allowedWorkflows: ["update"],
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
          if (item.title === 'Fishable creeks streams rivers'){
            item.title = 'Fishable Rivers, Streams and Creeks'
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
      // map.add(nhdLayer)
      map.add(fishableWatersLayer)

      // Filter fishable waters by ndow_responsibility
      // fishableWatersLayer.when(() => {
      //   fishableWatersLayer.definitionExpression = "ndow_responsibility = '"+ IdentityManager.credentials[0].userId +"'"
      // })
    })

    // Unselect features
    function unselectFeature () {
      if (highlight) {
        highlight.remove();
      }
    }

    // When view is clicked
    view.on('click', () => {
      unselectFeature()
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
