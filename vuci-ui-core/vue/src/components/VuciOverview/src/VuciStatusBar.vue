<template>
    <div class="main">
        <slot name="Header">
            <div v-if="text">{{ text }}:  ({{ currFormat() }})</div>
        </slot>
        <a-progress
        :percent="currVal"
        :showInfo="false"
        :stroke-width="strokewidth"
        :status="status"
        />
    </div>
</template>
<script>
export default {
  props: {
    text: {
      type: String
    },
    status: {
      type: String,
      default: 'normal'
    },
    number: {
      type: Number,
      required: true
    },
    maxnumber: {
      type: Number,
      default: 100
    },
    unit: {
      type: String,
      default: '%'
    },
    strokewidth: {
      type: Number,
      default: 10
    }
  },
  computed: {
    currVal () {
      return Number(((this.number / this.maxnumber) * 100).toFixed(0))
    },
    currFormat () {
      return this.unit === '%'
        ? () => {
          return `${this.currVal}%`
        }
        : () => {
          return `${this.number} ${this.unit}`
        }
    }
  }
}
</script>
<style>
.main{
    display: flex;
    flex-direction: column;
    align-items: center;
}

</style>
