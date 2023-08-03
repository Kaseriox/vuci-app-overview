<template>
        <VuciCard :title="'SYSTEM'">
          <div style="color: #0054a6;" slot="extra">
            <VuciStatusBar :text="'CPU load'" :number="CPU.percent"/>
          </div>
          <div slot="content">
            <VuciList :data="list">
              <div class="usage" slot="MEMORY USAGE">
                <VuciStatusBar :text="'RAM'" :number="RAM.available" :maxnumber="RAM.total"/>
                <VuciStatusBar :text="'FLASH'" :number="FLASH.available" :maxnumber="FLASH.total"/>
              </div>
            </VuciList>
          </div>
        </VuciCard>
  </template>
<script>
export default {
  timers: {
    SetData: { time: 5000, autostart: true, immediate: true, repeat: true }
  },
  data () {
    return {
      list: [],
      RAM: {
        available: 1,
        total: 1
      },
      FLASH: {
        available: 1,
        total: 1
      },
      CPU: {
        lastCPUTime: null,
        percent: 100
      }
    }
  },
  methods: {
    async SetData () {
      const SystemInfo = await this.$system.getSystemInfo()
      const BoardInfo = await this.$system.getBoardInfo()
      const DiskInfo = await this.$system.getDiskInfo()

      const time = new Date(SystemInfo.uptime * 1000).toISOString().slice(11, 19)
      const Localtime = new Date(0)
      Localtime.setMilliseconds(SystemInfo.localtime * 1000)

      this.RAM.total = SystemInfo.memory.total
      this.RAM.available = this.RAM.total - SystemInfo.memory.available

      this.FLASH.total = DiskInfo.root.total
      this.FLASH.available = this.FLASH.total - DiskInfo.root.free

      await this.UpdateCPU()

      this.list = []
      this.list.push({ title: 'ROUTER UPTIME', content: `${time.substring(0, 2)}h ${time.substring(3, 5)}m ${time.substring(6, 8)}s` })
      this.list.push({ title: 'LOCAL DEVICE TIME', content: `${Localtime.getFullYear()}-${Localtime.getMonth() + 1 < 10 ? '0' + `${Localtime.getMonth() + 1}` : `${Localtime.getMonth()}`}-${Localtime.getMonth() + 1 < 10 ? '0' + `${Localtime.getDate() + 1}` : `${Localtime.getDate()}`} ${Localtime.getHours() < 10 ? '0' + `${Localtime.getHours()}` : `${Localtime.getHours()}`}:${Localtime.getMinutes() < 10 ? '0' + `${Localtime.getMinutes()}` : `${Localtime.getMinutes()}`}:${Localtime.getSeconds() < 10 ? '0' + `${Localtime.getSeconds()}` : `${Localtime.getSeconds()}`} ` })
      this.list.push({ title: 'MEMORY USAGE', content: '' })
      this.list.push({ title: 'FIRMWARE VERSION', content: `${BoardInfo.release.distribution} ${BoardInfo.release.version}` })
    },
    async UpdateCPU () {
      const times = await this.$rpc.call('system', 'cpu_time')
      if (!this.CPU.lastCPUTime) {
        this.CPU.percent = 0
        this.CPU.lastCPUTime = times
      }
      const idle1 = this.CPU.lastCPUTime[3]
      const idle2 = times[3]
      let total1 = 0
      let total2 = 0
      this.CPU.lastCPUTime.forEach(t => {
        total1 += t
      })
      times.forEach(t => {
        total2 += t
      })
      this.CPU.percent = Math.floor(((total2 - total1 - (idle2 - idle1)) / (total2 - total1)) * 100) || 0
      this.CPU.lastCPUTime = times
    }
  }
}
</script>
<style scoped>
.usage{
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  gap: 10px;
  color: #0054a6;
}
</style>
