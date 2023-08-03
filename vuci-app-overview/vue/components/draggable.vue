<template>
    <div ref="draggable_container" id="container" @mousedown.prevent="DragMouseDown">
        <slot />
    </div>
  </template>
<script>
export default {
  props: {
    left: {
      type: Number,
      default: 0
    },
    top: {
      type: Number,
      default: 0
    },
    name: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      positions: {
        clientX: undefined,
        clientY: undefined,
        movementX: 0,
        movementY: 0
      }
    }
  },
  methods: {
    DragMouseDown (e) {
      this.positions.clientX = e.clientX
      this.positions.clientY = e.clientY
      document.onmousemove = this.Drag
      document.onmouseup = this.closeDragElement
    },
    Drag (e) {
      this.positions.movementX = this.positions.clientX - e.clientX
      this.positions.movementY = this.positions.clientY - e.clientY
      this.positions.clientX = e.clientX
      this.positions.clientY = e.clientY
      this.$refs.draggable_container.style.top = Math.max(0, Math.min(this.ContainerHeigth(), (this.$refs.draggable_container.offsetTop - this.positions.movementY))) + 'px'
      this.$refs.draggable_container.style.left = Math.max(0, Math.min(this.ContainerWidth(), (this.$refs.draggable_container.offsetLeft - this.positions.movementX))) + 'px'
    },
    closeDragElement () {
      this.SetTopLeft()
      document.onmouseup = null
      document.onmousemove = null
    },
    ResizeHandler (e) {
      document.onmouseup = null
      document.onmousemove = null
      this.$refs.draggable_container.style.top = Math.max(0, Math.min(this.ContainerHeigth(), this.$refs.draggable_container.offsetTop)) + 'px'
      this.$refs.draggable_container.style.left = Math.max(0, Math.min(this.ContainerWidth(), this.$refs.draggable_container.offsetLeft)) + 'px'
    },
    ContainerHeigth () {
      return this.$refs.draggable_container.parentElement.parentElement.scrollHeight - this.$refs.draggable_container.scrollHeight
    },
    ContainerWidth () {
      return this.$refs.draggable_container.parentElement.scrollWidth - this.$refs.draggable_container.scrollWidth
    },
    async SetTopLeft () {
      await this.$uci.load('overview')
      await this.$uci.set('overview', this.name, 'top', this.$refs.draggable_container.style.top)
      await this.$uci.set('overview', this.name, 'left', this.$refs.draggable_container.style.left)
      await this.$uci.save()
    }
  },
  mounted () {
    window.addEventListener('resize', this.ResizeHandler)
    this.$refs.draggable_container.style.top = this.top + 'px'
    this.$refs.draggable_container.style.left = this.left + 'px'
  },
  unmounted () {
    window.removeEventListener('resize', this.ResizeHandler)
  }
}
</script>
<style>
#container{
    position: absolute;
    z-index: 10;
    cursor: move;
}
</style>
