<template>
  <VuciCard :title="type.toUpperCase()">
    <div slot="content">
      <VuciList :data="list" />
    </div>
  </VuciCard>
</template>
<script>
export default {
  props: {
    type: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      list: []
    }
  },
  methods: {
    async SetData () {
      await this.$network.load()
      const LAN = (await this.$network.getInterfaces()).filter(item => item.name === this.type)[0]

      if (!LAN.status.up) {
        this.list = [{ title: 'NOT CONNECTED', content: 'NOT CONNECTED' }]
        return
      }
      this.list = []
      this.list.push({ title: 'TYPE', content: `${LAN.status.device}` })
      for (const item of LAN.status['ipv4-address']) {
        this.list.push({ title: 'IP ADDRESS', content: `${item.address}/${item.mask}` })
      }
    }
  },
  mounted () {
    this.SetData()
  }
}
</script>
