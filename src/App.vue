<template>
  <div id="app">
    <md-app style="height: 100vh;">
      <md-app-drawer md-permanent="full">
        <Sidebar :key="componentKey" :constants="labels" :data="list" @goToLocation = "goToLocation"/>
      </md-app-drawer>
      <md-app-content>
        <Map @loadSidebar = "loadSidebar" :geo="geo"/>
      </md-app-content>
    </md-app>
  </div>
</template>

<script>
import Sidebar from './components/Sidebar'
import Map from './components/Map'
import ConstObj from './Plugins/Constants'

export default {
  components: { Sidebar, Map, ConstObj },
  data () {
    return {
      list: [],
      componentKey: 0,
      labels: ConstObj,
      geo: []
    }
  },
  methods: {
    loadSidebar (arr) {
      if (this.list.length === 0) {
        this.list = arr
        this.componentKey += 1
      }
    },
    goToLocation (arr) {
      this.geo = arr
    }
  }
}
</script>
