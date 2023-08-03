<template>
    <VuciCard :title="'WAN'">
      <div slot="content">
        <VuciList :data="list" />
      </div>
    </VuciCard>
  </template>
<script>
export default {
  data () {
    return {
      list: []
    }
  },
  methods: {
    async SetData () {
      await this.$network.load()
      const WAN = (await this.$network.getInterfaces()).filter(item => item.name === 'wan')[0]
      if (!WAN.status.up) {
        this.list = [{ title: 'NOT CONNECTED', content: 'NOT CONNECTED' }]
        return
      }
      this.list = []
      this.list.push({ title: 'TYPE', content: `${WAN.status.device}` })
      for (const item of WAN.status['ipv4-address']) {
        this.list.push({ title: 'IP ADDRESS', content: `${item.address}/${item.mask}` })
      }
    }
  },
  mounted () {
    this.SetData()
  }
}
</script>
