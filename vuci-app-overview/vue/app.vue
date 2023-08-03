<template>
  <div>

    <Drawer :title="'Card View Toggle'" @DrawerClosed="GetCards">
      <CardToggle slot="content" :data="sections"/>
    </Drawer>
    <div class="drag-container">
        <Draggable v-for="item in FilterCards" :left="parseInt(item.left)" :top="parseInt(item.top)" :name="item['.name']" :key="item['.name']">
            <component :is="item.component"/>
        </Draggable>
    </div>

  </div>
</template>
<script>

import Draggable from './components/draggable.vue'
import SystemCard from './components/SystemCard.vue'
import LanCard from './components/LanCard.vue'
import WanCard from './components/WanCard.vue'
import NetworkEventsCard from './components/NetworkEventsCard.vue'
import SystemEventsCard from './components/SystemEventsCard.vue'
import Drawer from './components/Drawer.vue'
import CardToggle from './components/CardToggle.vue'
export default {
  components: { Draggable, SystemCard, LanCard, WanCard, NetworkEventsCard, SystemEventsCard, Drawer, CardToggle },
  data () {
    return {
      sections: [
        { name: 'system', title: 'Toggle System Visibility' },
        { name: 'lan', title: 'Toggle LAN Visibility' },
        { name: 'wan', title: 'Toggle WAN Visibility' },
        { name: 'networkevents', title: 'Toggle Network Events Visibility' },
        { name: 'systemevents', title: 'Toggle System Events Visibility' }
      ],
      cards: {
        system: { component: SystemCard },
        lan: { component: LanCard },
        wan: { component: WanCard },
        networkevents: { component: NetworkEventsCard },
        systemevents: { component: SystemEventsCard }
      }
    }
  },
  methods: {
    async GetCards () {
      await this.$uci.load('overview')
      for (const item of this.sections) {
        const section = await this.$uci.get('overview', item.name)
        this.cards[section['.name']] = { ...this.cards[section['.name']], ...section }
      }
    }
  },
  computed: {
    FilterCards () {
      const result = {}
      for (const [key, val] of Object.entries(this.cards)) {
        if (val.visibility === '1') {
          result[key] = val
        }
      }
      return result
    }
  },
  created () {
    this.GetCards()
  }
}
</script>
<style scoped>
.drag-container{
  position: relative;
}
</style>
