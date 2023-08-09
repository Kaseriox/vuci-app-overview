<template>
    <VuciCard :title="'RECENT SYSTEM EVENTS'">
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
      const events = (await this.$log.get({ table: 'SYSTEM', limit: 4, order: 'DESC' })).log
      this.list = []
      for (const item of events) {
        const date = new Date(0)
        date.setMilliseconds(item.TIME * 1000)
        this.list.push({ title: `${date.getFullYear()}-${date.getMonth() + 1 < 10 ? '0' + `${date.getMonth() + 1}` : `${date.getMonth()}`}-${date.getDate() + 1 < 10 ? '0' + `${date.getDate() + 1}` : `${date.getDate()}`} ${date.getHours() < 10 ? '0' + `${date.getHours()}` : `${date.getHours()}`}:${date.getMinutes() < 10 ? '0' + `${date.getMinutes()}` : `${date.getMinutes()}`}:${date.getSeconds() < 10 ? '0' + `${date.getSeconds()}` : `${date.getSeconds()}`}`, content: item.TEXT })
      }
    }
  },
  mounted () {
    this.SetData()
  }
}
</script>
