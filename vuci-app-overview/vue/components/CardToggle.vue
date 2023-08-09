<template>
    <vuci-form uci-config="overview" @applied="ApplyChanges()">
      <vuci-named-section v-for="item in data" :name="item['.name']" v-slot="{ s }" :card="false" :key="item['.name']">
        <vuci-form-item-switch :uci-section="s" :label="`Toggle ${(item['.name']).toUpperCase()} Visibility`" name="visibility" true-value="1" false-value="0"/>
      </vuci-named-section >
    </vuci-form>
  </template>
<script>
export default {
  data () {
    return {
      data: []
    }
  },
  methods: {
    ApplyChanges () {
      this.$emit('refresh')
    },
    async GetData () {
      await this.$uci.load('overview')
      this.data = await this.$uci.sections('overview')
    }
  },
  created () {
    this.GetData()
  }
}
</script>
