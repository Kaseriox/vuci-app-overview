<template>
  <div class="example">
    <a-input v-model="time"/>
    <a-button type="primary" @click="get_time">
      Get time
    </a-button>
  </div>
</template>

<script>
export default {
  data () {
    return {
      time: ''
    }
  },
  methods: {
    get_time () {
      this.$rpc.call('example', 'get_time', { }).then(r => {
        this.time = r.time
      })
    }
  }
}
</script>
