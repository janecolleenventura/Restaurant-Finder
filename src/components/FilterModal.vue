<template>
  <div>
    <md-dialog md-active class="md-default">
      <md-dialog-title>Filters</md-dialog-title>
      <md-content>
        <div class="full-control">
          <md-list>
            <md-subheader>Categories</md-subheader>
            <div class="custom-checkbox" v-for="(label, key) in Constants.CATEGORY" :key="key">
              <md-checkbox v-model="formObj.categories[key]"/>
              <span class="md-list-item-text">{{ label }}</span>
            </div>
          </md-list>
          <md-list>
            <md-subheader>Cuisines</md-subheader>
            <div class="custom-checkbox" v-for="(label, key) in Constants.CUISINE" :key="key">
              <md-checkbox v-model="formObj.cuisines[key]"/>
              <span class="md-list-item-text">{{ label }}</span>
            </div>
          </md-list>
          <md-list>
            <md-subheader>Location</md-subheader>
            <div class="custom-checkbox" v-for="(label, key) in Constants.LOCATION" :key="key">
              <md-checkbox v-model="formObj.location[key]"/>
              <span class="md-list-item-text">{{ label }}</span>
            </div>
          </md-list>
        </div>
        <md-dialog-actions>
          <md-button class="md-accent md-raised" @click="close()">Close</md-button>
          <md-button class="md-primary md-raised" @click="submitData()">Save</md-button>
        </md-dialog-actions>
      </md-content>
    </md-dialog>
  </div>
</template>

<script>
import ConstObj from '../Plugins/Constants'
export default {
  components: { ConstObj },
  data () {
    return {
      formObj: {
        categories: [7],
        cuisines: [14],
        location: [5]
      },
      Constants: ConstObj
    }
  },
  methods: {
    close () {
      this.$emit('closeModal')
    },
    submitData () {
      this.$emit('addFilter', this.formObj)
      this.close()
    }
  }
}
</script>

<style scoped>
  .full-control > .md-list {
    width: 320px;
    max-width: 100%;
    height: 500px;
    display: inline-block;
    overflow: auto;
    border: 1px solid rgba(#000, .12);
    vertical-align: top;
  }

</style>
