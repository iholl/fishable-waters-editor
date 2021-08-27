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

    const fishableWatersBodiesLayer = new FeatureLayer({
      url: 'https://services.arcgis.com/RyxlXSfFi87rAosq/arcgis/rest/services/fishable_lakes_reservoirs_ponds/FeatureServer/0',
      id: 'Fishable Lakes, Reservoirs, Urban Bodies',
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

      const yesNo = {
        type: "coded-value",
        name: 'geo_update',
        codedValues: [
          { name: "Yes", code: "yes" },
          { name: "No", code: "no" }
        ]
      }

      const speicesValues = {
        type: "coded-value",
        codedValues: [
          { name: "White Bass", code: "white bass" },
          { name: "Largemounth Bass", code: "largemouth bass" },
          { name: "Smallmouth Bass", code: "smallmouth bass" },
          { name: "Spotted Bass", code: "spotted bass" },
          { name: "Striped Bass", code: "striped bass" },
          { name: "Carp", code: "carp" },
          { name: "Bullhead Catfish", code: "bullhead catfish" },
          { name: "Channel Catfish", code: "channel catfish" },
          { name: "White Catfish", code: "white catfish" },
          { name: "Black Crappie", code: "black crappie" },
          { name: "White Crappie", code: "white crappie" },
          { name: "Yellow Perch", code: "yellow perch" },
          { name: "White Bass", code: "white bass" },
          { name: "Kokanee Salmon", code: "kokanee salmon" },
          { name: "Bluegill Sunfish", code: "bullgill sunfish" },
          { name: "Green Sunfish", code: "green sunfish" },
          { name: "Pumpkinseed", code: "pumpkinseed" },
          { name: "Redear Sunfish", code: "redear sunfish" },
          { name: "Brook Trout", code: "brook trout" },
          { name: "Brown Trout", code: "brown trout" },
          { name: "Bull Trout", code: "bull trout" },
          { name: "Mackinaw Trout", code: "mackinaw trout" },
          { name: "Raibow Trout", code: "rainbow trout" },
          { name: "Bowcutt Trout", code: "bowcutt trout" },
          { name: "Tiger Trout", code: "tiger trout" },
          { name: "Walleye", code: "walleye" },
          { name: "Wiper", code: "wiper" },
          { name: "Lahontan Cutthroat Trout", code: "lahontan cutthroat trout" },
          { name: "Redband Trout", code: "redband trout" },
          { name: "Tiger Muskie", code: "tiger muskies" },
          { name: "Yellowstone Cutthroat Trout", code: "yellowstone cutthroat trout" },
          { name: "Mountain Whitefish", code: "mountain whitefish" },
          { name: "Sacramento Perch", code: "Sacramento perch" }
        ]
      }

      // Editor Fishable Waters fields
      const fishableWaterFields = {
        layer: fishableWatersLayer,
        fieldConfig: [
        {
          name: 'geo_update',
          label: 'Update Geometry',
          domain: yesNo
        },
        {
          name: 'species_1',
          label: 'Species 1',
          domain: speicesValues
        },
        {
          name: 'species_2',
          label: 'Species 2',
          domain: speicesValues
        },
        {
          name: 'species_3',
          label: 'Species 3',
          domain: speicesValues
        },
        {
          name: 'species_4',
          label: 'Species 4',
          domain: speicesValues
        },
        {
          name: 'species_5',
          label: 'Species 5',
          domain: speicesValues
        },
        {
          name: 'species_6',
          label: 'Species 6',
          domain: speicesValues
        },
        {
          name: 'species_7',
          label: 'Species 7',
          domain: speicesValues
        },
        {
          name: 'species_8',
          label: 'Species 8',
          domain: speicesValues
        },
        {
          name: 'species_9',
          label: 'Species 9',
          domain: speicesValues
        },
        {
          name: 'species_10',
          label: 'Species 10',
          domain: speicesValues
        },
        {
          name: 'species_11',
          label: 'Species 11',
          domain: speicesValues
        }]
      }

      // Editor
      const editor = new Editor({
        view: view,
        allowedWorkflows: ["update"],
        layerInfos: [
          { 
            layer: fishableWatersLayer,
            fieldConfig: [fishableWaterFields] 
          },
          { 
            layer: fishableWatersBodiesLayer,
            fieldConfig: [fishableWaterFields] 
          }
        ],
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
      map.add(fishableWatersBodiesLayer)

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
