<template>
    <div>
      <md-toolbar class="md-primary md-active">
          <h3 class="md-title">Restaurant Locator</h3>
          <div class="btn-holder">
            <md-button class="icon-btn" @click="showModal = true">
              <span class="material-icons">
                filter_alt
              </span>
            </md-button>
            <md-button class="icon-btn" @click="resetFilter">
              <span class="material-icons">
              view_headline
              </span>
            </md-button>
          </div>
      </md-toolbar>
      <md-list class="md-triple-line" v-for="row in list" :key="row.id">
        <md-list-item>
          <div class="md-list-item-text">
            <a @click="setCoordinates(row.geometry)">
              <span>{{ row.title }}</span>
              <span>{{ constants.CUISINE[row.cuisine] }}</span>
              <p>{{ constants.LOCATION[row.location] }}</p>
            </a>
          </div>
        </md-list-item>

        <md-divider class="md-inset"></md-divider>
      </md-list>
      <FilterModal v-if="showModal" @closeModal="closeModal" @addFilter="addFilter"/>
    </div>
</template>

<script>
import FilterModal from '../components/FilterModal'
export default {
  props: {
    data: Array,
    constants: Object
  },
  components: {
    FilterModal
  },
  data () {
    return {
      showModal: false,
      list: this.data
    }
  },
  methods: {
    setCoordinates (geo) {
      this.$emit('goToLocation', geo)
    },
    closeModal () {
      this.showModal = false
    },
    addFilter (data) {
      let cg = data.categories
      let cs = data.cuisines
      let loc = data.location
      let i = 0
      let newList = []
      let found = false

      for (i = 1; i <= cg.length; i++) {
        this.data.filter(function (elem) {
          found = newList.some(el => el.id === elem.id)
          if (!found && cg[i] === true && i === elem.category) {
            newList.push(elem)
          }
        })
      }
      for (i = 1; i <= cs.length; i++) {
        this.data.filter(function (elem) {
          found = newList.some(el => el.id === elem.id)
          if (!found && cs[i] === true && i === elem.cuisine) {
            newList.push(elem)
          }
        })
      }
      for (i = 1; i <= loc.length; i++) {
        this.data.filter(function (elem) {
          found = newList.some(el => el.id === elem.id)
          if (!found && loc[i] === true && i === elem.location) {
            newList.push(elem)
          }
        })
      }
      this.list = newList
    },
    resetFilter () {
      this.list = this.data
    }
  }
}
</script>
