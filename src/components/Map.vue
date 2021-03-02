<template>
    <div>
        <mapbox
        access-token='pk.eyJ1IjoiamFuZWNvbGxlZW52ZW50dXJhIiwiYSI6ImNrbG50NHBhNTBtY24ydWw2d3FrOHEzNzIifQ.pGFk4OFUehABAB9dxmTJ6Q'
        :map-options="{
            container: 'map',
            style: 'mapbox://styles/mapbox/streets-v11',
            center: [123.8854, 10.3157],
            zoom: 11.3
        }"
        @map-load="loaded"
        @map-sourcedata="loadList"
        />
    </div>
</template>

<script>
import Mapbox from 'mapbox-gl-vue'
import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder'

export default {
  components: { MapboxGeocoder, Mapbox },
  props: {
    geo: Array
  },
  data () {
    return {
      coordinates: this.geo,
      mapBx: {}
    }
  },
  methods: {
    loaded (map) {
      this.mapBx = map
      map.addSource('data', {
        type: 'geojson',
        data: 'static/features.geojson'
      })

      map.addLayer({
        id: 'resto',
        type: 'circle',
        source: 'data',
        paint: {
          'circle-color': 'orange',
          'circle-radius': [
            'step',
            ['get', 'point_count'],
            20,
            5,
            30,
            8,
            40
          ]
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
    },

    loadList (map) {
      let obj = {}
      let prop = {}
      let list = []
      let found = false
      let geometry = {}
      if (map.getSource('data') && map.isSourceLoaded('data')) {
        let features = map.querySourceFeatures('data')
        features.forEach(function (store) {
          prop = store.properties
          geometry = store.geometry
          obj = {}
          found = list.some(el => el.id === prop.ID)
          if (list.length === 0 || !found) {
            obj.id = prop.ID
            obj.title = prop.Title
            obj.location = prop.Location
            obj.cuisine = prop.Cuisine
            obj.category = prop.Category
            obj.geometry = geometry.coordinates
            list.push(obj)
          }
        })
        if (list.length > 0) {
          this.$emit('loadSidebar', list)
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
