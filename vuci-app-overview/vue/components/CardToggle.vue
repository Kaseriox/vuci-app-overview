<template>
    <vuci-form uci-config="overview">
      <vuci-named-section v-for="item in data" :name="item.name" v-slot="{ s }" :card="false" :key="item.name">
        <vuci-form-item-switch :uci-section="s" :label="item.title" name="visibility" true-value="1" false-value="0"/>
      </vuci-named-section >
    </vuci-form>
  </template>
<script>
export default {
  props: {
    data: {
      type: Array,
      required: true
    }
  }
}
</script>
