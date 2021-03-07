<template>
    <div>
        <mapbox
        access-token='pk.eyJ1IjoiamFuZWNvbGxlZW52ZW50dXJhIiwiYSI6ImNrbG50NHBhNTBtY24ydWw2d3FrOHEzNzIifQ.pGFk4OFUehABAB9dxmTJ6Q'
        :map-options="{
            container: 'map',
            style: 'mapbox://styles/janecolleenventura/cklntbbu20i0a17pj9o4jvfje',
            center: [123.8854, 10.3157],
            zoom: 12
        }"
        @map-load="loaded"
        />
    </div>
</template>

<script>
import Mapbox from 'mapbox-gl-vue'
import mapboxgl from 'mapbox-gl/dist/mapbox-gl'
import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder'

export default {
  components: { MapboxGeocoder, Mapbox, mapboxgl },
  props: {
    geo: Array
  },
  data () {
    return {
      coordinates: this.geo,
      mapBx: {},
      locationList: [],
      locator: {}
    }
  },
  methods: {
    loaded (map) {
      this.mapBx = map
      let global = this
      map.addSource('data', {
        type: 'geojson',
        data: 'static/features.geojson'
      })

      map.addLayer({
        id: 'points',
        type: 'circle',
        source: 'data',
        paint: {
          'circle-color': 'white',
          'circle-radius': 25,
          'circle-opacity': 0
        }
      })
      /* eslint-disable no-new */
      map.addControl(new MapboxGeocoder({
        accessToken: 'pk.eyJ1IjoiamFuZWNvbGxlZW52ZW50dXJhIiwiYSI6ImNrbG50NHBhNTBtY24ydWw2d3FrOHEzNzIifQ.pGFk4OFUehABAB9dxmTJ6Q',
        mapboxgl: map,
        marker: false,
        placeholder: 'Search for restaurants',
        bbox: [123.81571041619664, 10.322879581550891, 124.04513379288458, 10.477085501223165],
        proximity: {
          longitude: 123.8858,
          latitude: 10.3157
        }
      }))

      let xmlhttp = new XMLHttpRequest()
      let url = 'static/features.geojson'

      xmlhttp.onreadystatechange = function () {
        if (this.readyState === 4 && this.status === 200) {
          let myArr = JSON.parse(this.responseText)
          global.loadList(myArr.features, global.mapBx)
        }
      }
      xmlhttp.open('GET', url, true)
      xmlhttp.send()
    },

    loadList (list, map) {
      let obj = {}
      let prop = {}
      let global = this
      let geometry = {}

      if (global.locationList.length === 0) {
        list.forEach(function (store) {
          prop = store.properties
          geometry = store.geometry
          new mapboxgl.Marker().setLngLat(geometry.coordinates).addTo(map)
          obj = {}
          obj.id = prop.ID
          obj.title = prop.Title
          obj.location = prop.Location
          obj.cuisine = prop.Cuisine
          obj.category = prop.Category
          obj.geometry = geometry.coordinates
          global.locationList.push(obj)
        })
        if (global.locationList.length > 0) {
          this.$emit('loadSidebar', global.locationList)
        }
      }
    },
    goToLocation (map, geo) {
      map.flyTo({
        center: geo,
        zoom: 20
      })
    }
  },
  watch: {
    geo: function (newCoordinates) {
      this.coordinates = newCoordinates
      this.goToLocation(this.mapBx, newCoordinates)
    }
  }
}
</script>
