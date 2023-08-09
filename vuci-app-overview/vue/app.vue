<template>
  <div>
    <Drawer :title="'Card View Toggle'">
        <CardToggle slot="content" @refresh="GetData"/>
    </Drawer>
    <div v-if="sections" class="cards">
      <div v-for="(item,idx) in sections" :key="item['.name']" class="card"
        draggable="true" @dragstart=DragStart($event,idx)
        @drop="OnDrop($event,idx)"
        @dragenter.prevent
        @dragover.prevent>
        <component :is="item.component" :type="item['.name']"/>
      </div>
    </div>
  </div>

</template>
<script>
import Drawer from './components/Drawer.vue'
import CardToggle from './components/CardToggle.vue'
import NetworkCard from './components/NetworkCard.vue'
import NetworkEventsCard from './components/NetworkEventsCard.vue'
import SystemCard from './components/SystemCard.vue'
import SystemEventsCard from './components/SystemEventsCard.vue'

export default {
  components: { Drawer, CardToggle, NetworkCard, NetworkEventsCard, SystemCard, SystemEventsCard },
  data () {
    return {
      sections: false
    }
  },
  methods: {
    async CreateNetworkInterfaces () {
      await this.$uci.load('network')
      let interfaces = await this.$uci.sections('network')
      interfaces = interfaces.filter(item => item['.type'] === 'interface').filter(item => item['.name'] !== 'loopback')
      await this.$uci.load('overview')
      const dropzones = await this.FindDropZone()
      let sections = await this.$uci.sections('overview')
      sections = sections.filter(item => item.component === 'NetworkCard')
      for (const item of sections) {
        if (interfaces.find(f => f['.name'] === item['.name']) === undefined) {
          await this.$uci.del('overview', item['.name'])
        }
      }
      await this.$uci.save()
      for (const interf of interfaces) {
        await this.$uci.load('overview')
        if (await this.$uci.get('overview', interf['.name']) === undefined) {
          await this.$uci.add('overview', 'section', interf['.name'])
          await this.$uci.load('overview')
          await this.$uci.set('overview', interf['.name'], 'component', 'NetworkCard')
          await this.$uci.set('overview', interf['.name'], 'dropzone', dropzones[0])
          await this.$uci.set('overview', interf['.name'], 'visibility', 0)
          await this.$uci.save()
          dropzones.splice(0, 1)
        }
      }
    },
    async GetData () {
      await this.$uci.load('overview')
      const data = await this.$uci.sections('overview')
      this.sections = data.filter(item => parseInt(item.visibility) === 1)
      this.SetSectionData()
    },
    async FindDropZone () {
      const arr = []
      await this.$uci.load('overview')
      const sections = this.$uci.sections('overview')
      for (let i = 1; i < 9999; i++) {
        if (sections.find(item => parseInt(item.dropzone) === i) === undefined) {
          arr.push(i)
        }
      }
      return arr
    },
    DragStart (event, index) {
      event.dataTransfer.dropEffect = 'move'
      event.dataTransfer.effectAllowed = 'move'
      event.dataTransfer.setData('itemIndex', index)
    },
    async OnDrop (event, dropzone) {
      await this.$uci.load('overview')
      const DragItemIndex = event.dataTransfer.getData('itemIndex')
      const temp = this.sections[dropzone].dropzone
      this.sections[dropzone].dropzone = this.sections[DragItemIndex].dropzone
      await this.$uci.set('overview', this.sections[dropzone]['.name'], 'dropzone', this.sections[DragItemIndex].dropzone)
      this.sections[DragItemIndex].dropzone = temp
      await this.$uci.set('overview', this.sections[DragItemIndex]['.name'], 'dropzone', temp)
      await this.$uci.save()
      this.SetSectionData()
    },
    SetSectionData () {
      this.sections = this.sections.sort(function (a, b) {
        return parseFloat(a.dropzone) - parseFloat(b.dropzone)
      })
    },
    async CreateDefaultCard () {
      const dropzones = await this.FindDropZone()
      const items = [
        { name: 'system', dropzone: dropzones[0], component: 'SystemCard' },
        { name: 'systemevents', dropzone: dropzones[1], component: 'NetworkEventsCard' },
        { name: 'networkevents', dropzone: dropzones[2], component: 'SystemEventsCard' }
      ]

      for (const item of items) {
        await this.$uci.load('overview')
        if (await this.$uci.get('overview', item.name) === undefined) {
          this.$uci.add('overview', 'section', item.name)
          await this.$uci.set('overview', item.name, 'component', item.component)
          await this.$uci.set('overview', item.name, 'dropzone', item.dropzone)
          await this.$uci.set('overview', item.name, 'visibility', 0)
        }
      }
    }
  },
  async created () {
    await this.CreateDefaultCard()
    await this.CreateNetworkInterfaces()
    await this.GetData()
  },
  destroyed () {
    this.sections = false
  }

}
</script>
<style scoped>
.cards{
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 20px 20px
}
</style>
